# Repo Review → Phase Plan — LedgerSync (Worked Example)

> Worked example showing how the [LedgerSync review](poc-to-production-review-example.md) is converted into a hardening plan using the [repo review to phase plan template](../templates/repo-review-to-phase-plan.md).
> The project is fictional. Secrets are shown as `<REDACTED>` placeholders only.

Converts the LedgerSync repo review findings into a phase-gated, vertical-slice hardening plan. Used with [POC-to-production hardening mode](../docs/poc-to-production-mode.md).

---

## Source material

- Review document(s): [LedgerSync repo review](poc-to-production-review-example.md)
- Reviewers / date: senior engineering review, this hardening pass
- Codebase / branch reviewed: `ledgersync @ poc-main`
- Other inputs: none (no prior incidents; this is the first formal review)

## Current project state

LedgerSync is a two-week POC that runs a fetch → reconcile → report pipeline: it pulls invoices from the vendor billing API, reconciles them against the internal `payments` table, and emails a daily mismatch report. The happy path works and the matching logic is correct against clean data. It is run manually today. It has no tests, no structured logging, a hardcoded API token, and writes to `payments` without a transaction boundary.

## Target production state

LedgerSync runs as an unattended daily job that:

- holds no secrets in source or git history
- aborts loudly rather than reporting against partial data
- updates `payments` atomically, with a clean recovery path on failure
- emits traceable logs and fails when the report does not send
- has automated coverage for the reconciliation matching rules

## Finding classification

Classify every finding. Not every finding becomes a slice. See [classification guidance](../docs/poc-to-production-mode.md#classifying-repo-review-findings).

| # | Finding | Classification | Disposition |
|---|---|---|---|
| 1 | Hardcoded vendor API token | Production blocker | Slice H1 |
| 2 | Swallowed fetch errors; partial data treated as complete | Production blocker | Slice H2 |
| 3 | Payment updates with no transaction boundary | Production blocker | Slice H3 |
| 4 | No timeout/retry on vendor call | Hardening requirement | Slice H2 |
| 5 | No structured logging | Hardening requirement | Slice O1 |
| 6 | Silent report-email failure | Hardening requirement | Slice O2 |
| 7 | No reconciliation tests | Hardening requirement | Slice T1 |
| 8 | Config mixed with logic | Maintainability improvement | Deferred — carry-forward |
| 9 | 200-line `run` function | Maintainability improvement | Adjacent to T1 if cheap; else deferred |
| 10 | Synchronous requests instead of async | Rejected / not valid | No action — adequate at expected volume |
| 11 | No multi-currency support | Deferred | Carry-forward; needs owner decision |
| 12 | Duplicate vendor invoice IDs | Accepted risk | Sign off; handled downstream |

## Core invariants

The rules the production system must hold throughout hardening:

- No secret appears in source or git history.
- The pipeline never reconciles against a known-partial invoice set — a fetch error aborts the run.
- Payment updates are atomic: a run either applies all matches or none.
- Every run is traceable (run ID, record counts) and a failed report send fails the job.
- Reconciliation matching behaviour does not change silently — it is covered by tests.

## Proposed phases

| Phase | Goal | Findings addressed |
|---|---|---|
| REVIEW | Confirm findings, classify, plan (complete — see review) | — |
| HARDEN | Remove credential exposure; fix partial-data and data-safety risks | #1, #2, #3, #4, #7 |
| OPERATE | Make the daily job observable and fail loudly | #5, #6 |
| CLOSEOUT | Confirm blockers resolved and accepted risks signed off; go/no-go | — |

## Proposed slices

Each slice is a narrow remediation unit tied to specific findings.

| Slice | Phase | Title | Findings | Outcome | Risk |
|---|---|---|---|---|---|
| H1 | HARDEN | Move secrets to environment/secret store | #1 | No credentials in source or history | Med |
| H2 | HARDEN | Fail loudly on fetch errors | #2, #4 | Partial fetches abort; timeout + bounded retry added | Med |
| H3 | HARDEN | Wrap payment updates in a transaction | #3 | Reconciliation is atomic and recoverable | High |
| T1 | HARDEN | Reconciliation matching tests | #7 (#9 if cheap) | Matching rules covered; safe to change | Med |
| O1 | OPERATE | Structured logging with run IDs and counts | #5 | Runs are traceable and alertable | Low |
| O2 | OPERATE | Surface report-delivery failure | #6 | Job fails when the report does not send | Low |

## Recommended first slice

**H1 — Move secrets to environment/secret store.** It removes a live credential from git history (highest severity), the change is bounded and low-coupling, and it gives everyone a clean baseline to do the remaining slices from. H3 is the highest-risk change and should follow once the team is working from that clean baseline, ideally after T1 gives the matching rules test coverage.

## Explicit non-goals

This hardening pass will not:

- rewrite the pipeline or move it to async (#10 rejected)
- add multi-currency support (#11 deferred pending owner decision)
- restructure `config.py` for its own sake (#8 deferred to carry-forward)
- add new product features beyond the existing reconcile-and-report loop
- change the reconciliation matching rules themselves — only cover them with tests

## Open questions

- Is multi-currency support (#11) required before go-live, or genuinely deferred?
- Where should the vendor token live in production — environment variable or org secret store? (Affects H1.)
- When the vendor API is fully down for a run, should the job skip-with-alert or hard-fail? (Affects H2's abort behaviour.)
- Should #9 (splitting the 200-line `run`) be folded into T1, or deferred to avoid widening that slice?
