# POC Repo Review — LedgerSync (Worked Example)

> Worked example of the [POC-to-production repo review prompt](../prompts/poc-to-production-repo-review.prompt.md).
> The project is fictional. It is detailed enough to show how findings are classified and turned into a hardening plan, but it is not a real codebase. Secrets are shown as `<REDACTED>` placeholders only.

**LedgerSync** is an internal tool that pulls invoice records from a vendor billing API, reconciles them against the internal `payments` table, and emails a daily mismatch report to the finance team. It was built as a one-engineer POC over two weeks and now needs to run unattended as a daily internal job.

---

## Executive summary

LedgerSync works end-to-end on the happy path: it fetches invoices, reconciles them, and produces a correct report against clean data. However, it is not safe to run unattended. A hardcoded API token, an unbounded fetch that silently drops records on API errors, and a reconciliation step that writes to the live `payments` table with no transaction boundary are the main risks. The core structure is sound — a fetch → reconcile → report pipeline — so hardening is viable without a rewrite. Recommend proceeding to hardening after the three production blockers are fixed.

## Production-readiness rating

**Prototype / not production-ready.**

The product loop is correct, but it carries a credential-exposure risk and a data-safety risk that would cause real incidents if run unattended. These are bounded, fixable issues rather than structural failures, so the rating is "prototype," not "high-risk / requires refactor."

## Review scope

- What was reviewed: the full `ledgersync/` package on branch `poc-main` — `fetch.py`, `reconcile.py`, `report.py`, `config.py`, and `main.py`.
- What was not reviewed or could not be verified: the vendor billing API contract (no sandbox access), the production SMTP path (no credentials available), and behaviour under real production data volume. Load and concurrency were not tested.
- How findings were checked: code read in full; the happy-path pipeline was run locally against a small fixture dataset. No production run was observed.

## Key findings

| # | Finding | Category | Confidence | Evidence (file/function) |
|---|---|---|---|---|
| 1 | Vendor API token hardcoded in source | Production blocker | Confirmed | `config.py:12` (`API_TOKEN = "<REDACTED>"`) |
| 2 | API fetch errors are swallowed; partial data treated as complete | Production blocker | Confirmed | `fetch.py:44-58` (`fetch_invoices`, bare `except`) |
| 3 | Reconciliation updates `payments` rows with no transaction boundary | Production blocker | Confirmed | `reconcile.py:71-96` (`apply_matches`) |
| 4 | No retry or timeout on the vendor API call | Hardening requirement | Confirmed | `fetch.py:38` (`requests.get`, no `timeout`) |
| 5 | No structured logging; only `print` to stdout | Hardening requirement | Confirmed | throughout `main.py`, `reconcile.py` |
| 6 | Report email failure is silent; job reports success regardless | Hardening requirement | Likely | `report.py:30-41` (`send_report`) |
| 7 | No tests for the reconciliation matching rules | Hardening requirement | Confirmed | no `tests/` directory present |
| 8 | Config values mixed with logic in `config.py` | Maintainability improvement | Confirmed | `config.py` |
| 9 | `reconcile.py` is a single 200-line function | Maintainability improvement | Confirmed | `reconcile.py:20-220` (`run`) |
| 10 | Uses synchronous requests rather than async | Rejected / not valid | Confirmed | `fetch.py` — adequate at expected volume |
| 11 | No multi-currency support | Deferred | Needs human decision | `reconcile.py` — out of current scope |
| 12 | Vendor occasionally returns duplicate invoice IDs | Accepted risk | Likely | observed in fixture; dedup handled downstream |

## Production blockers

**#1 — Hardcoded vendor API token.** The live token is committed in `config.py:12`. Anyone with repo access has the credential, and it is in git history. Production impact: credential compromise and unauthorised access to vendor billing data. Evidence: `config.py:12`.

**#2 — Swallowed fetch errors.** `fetch_invoices` (`fetch.py:44-58`) wraps the API call in a bare `except` that returns whatever was collected so far. A transient API error produces a *partial* invoice set that the rest of the pipeline treats as complete. Production impact: silent under-reporting — real mismatches go undetected on any day the API hiccups. Evidence: `fetch.py:44-58`.

**#3 — No transaction boundary on payment updates.** `apply_matches` (`reconcile.py:71-96`) updates `payments` rows in a loop with one commit per row. A crash mid-run leaves the table half-updated with no rollback. Production impact: inconsistent financial data and no clean recovery. Evidence: `reconcile.py:71-96`.

## Hardening requirements

- **#4 — No timeout/retry on the vendor call** (`fetch.py:38`). A hung connection hangs the whole daily job. Add a timeout and bounded retry.
- **#5 — No structured logging** (`main.py`, `reconcile.py`). `print` output cannot be collected or alerted on. An unattended job needs logs with run IDs and record counts.
- **#6 — Silent email failure** (`report.py:30-41`). If SMTP fails the job still exits 0, so finance silently receives no report. The job must surface delivery failure.
- **#7 — No reconciliation tests.** The matching rules are the core value and have no coverage. Any change risks a silent correctness regression.

## Maintainability improvements

- **#8 — Config mixed with logic** (`config.py`). Worth separating, but does not reduce production risk now.
- **#9 — 200-line `run` function** (`reconcile.py:20-220`). Hard to test as-is; splitting it would help #7, so it may be done adjacent to that work — but it is not a blocker on its own.

## Findings not worth fixing now

- **#10 — Synchronous requests (Rejected / not valid).** At the expected volume (a few thousand invoices/day) synchronous calls are fine. Async would add complexity with no production benefit. No action.
- **#11 — No multi-currency support (Deferred / needs human decision).** Valid future need, but out of scope for this hardening pass. Recorded in carry-forward; owner to confirm whether it is required before go-live.
- **#12 — Duplicate vendor invoice IDs (Accepted risk).** The vendor occasionally returns duplicates; the downstream dedup step already handles them. Real, understood, and tolerated. Recorded for sign-off.

## Evidence summary

Findings #1, #2, #3, #4, #5, #7, #8, #9, #10 were confirmed by reading the code directly, and the happy-path pipeline was run against fixtures. Finding #6 is rated *likely* rather than confirmed because the SMTP path could not be exercised without production credentials — the code clearly returns without checking the send result, but real failure behaviour was not observed. Finding #12 is *likely* (observed once in fixtures, not exhaustively characterised). No production run or production-volume test was performed; behaviour under real load is unverified.

## Recommended development decision

Proceed to hardening. The structure supports it and no rewrite is warranted. Fix the three production blockers (#1, #2, #3) before any unattended run, then schedule the hardening requirements. Do not bundle the maintainability improvements unless they fall naturally adjacent to a scheduled slice (notably #9 alongside the reconciliation tests in #7).

## Suggested hardening phases

| Phase | Goal | Findings addressed |
|---|---|---|
| REVIEW | Confirm findings, classify, plan (this document) | — |
| HARDEN | Remove credential exposure, fix data-safety and partial-data risks | #1, #2, #3, #4 |
| OPERATE | Make the daily job observable and fail loudly | #5, #6 |
| CLOSEOUT | Confirm blockers resolved, accepted risks signed off, go/no-go | — |

Reconciliation tests (#7) span HARDEN and OPERATE: add them alongside the changes they protect.

## Candidate remediation slices

| Slice | Phase | Title | Findings | Outcome | Risk |
|---|---|---|---|---|---|
| H1 | HARDEN | Move secrets to environment/secret store | #1 | No credentials in source or history | Med |
| H2 | HARDEN | Fail loudly on fetch errors | #2, #4 | Partial fetches abort the run; timeout + retry added | Med |
| H3 | HARDEN | Wrap payment updates in a transaction | #3 | Reconciliation is atomic and recoverable | High |
| O1 | OPERATE | Structured logging with run IDs and counts | #5 | Runs are traceable and alertable | Low |
| O2 | OPERATE | Surface report-delivery failure | #6 | Job fails when the report does not send | Low |
| T1 | HARDEN | Reconciliation matching tests | #7 (, #9) | Matching rules covered; safe to change | Med |

## Recommended first slice

**H1 — Move secrets to environment/secret store.** It is the highest-severity blocker (a live credential in history), the change is bounded and low-coupling, and it unblocks safe handling of the repo for everyone else doing the remaining slices. H3 is higher-risk to implement and should follow once the team is working from a clean baseline.

## Open questions

- Is multi-currency support (#11) required before go-live, or genuinely deferred?
- Where should the vendor token live in production — environment variable, or the org secret store?
- What is the acceptable behaviour when the vendor API is fully down for a daily run: skip with an alert, or hard-fail the job?

## Final recommendation

**Proceed to hardening after fixing the three production blockers.** LedgerSync's core reconciliation loop is correct and its structure is sound, so a rewrite is not justified. The credential exposure (#1), silent partial data (#2), and non-atomic payment updates (#3) must be resolved before any unattended production run; the remaining hardening and observability work can follow as bounded slices. Start with H1.
