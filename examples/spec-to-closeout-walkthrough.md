# Spec-to-Closeout Walkthrough — MiniLedger (Worked Example)

> A short, end-to-end walkthrough of one trip through the methodology — from a starting input to a phase closeout. It shows how the [docs](../docs/methodology.md), [templates](../templates/), [prompts](../prompts/), trackers, and implementation reports fit together.
>
> The project is fictional and the snippets are illustrative excerpts, not full templates. For complete templates, follow the links. For a fuller worked review and plan, see the [repo review example](poc-to-production-review-example.md) and [hardening plan example](poc-to-production-hardening-plan-example.md).

**MiniLedger** is a POC CSV invoice reconciler moving toward production. It already runs end-to-end; the goal is controlled hardening, so this uses [POC-to-production mode](../docs/poc-to-production-mode.md).

---

## 1. Starting input

The input here is a **repo review finding** (it could equally be an ambiguous spec). A review of MiniLedger produced several observations. Two examples:

- **F1** — CSV parser crashes on a missing `amount` column, taking down the whole run.
- **F2** — variable names in `parser.py` are inconsistent (`amt`, `total`, `value`).

## 2. Convert input into a phase plan

Classify findings before any become work — **not every finding becomes a blocker**. Using the [repo review to phase-plan template](../templates/repo-review-to-phase-plan.md):

| # | Finding | Classification | Disposition |
|---|---|---|---|
| F1 | Crash on missing `amount` column | Production blocker | Slice H1 |
| F2 | Inconsistent naming in `parser.py` | Maintainability improvement | Deferred — carry-forward |

F1 will cause incidents in production, so it becomes a slice. F2 is real but does not reduce production risk now, so it is recorded and deferred — not turned into work.

## 3. Choose a phase

The plan maps onto the hardening phase set. F1 lands in **HARDEN**:

| Phase | Goal | Findings |
|---|---|---|
| HARDEN | Remove the crash-on-bad-input risk | F1 |

## 4. Create a slice spec

One narrow slice for F1, using the [slice spec template](../templates/slice-spec.md):

```markdown
# Slice H1 — Validate CSV columns before reconciling
## Goal
A run with a malformed CSV fails cleanly with a clear error, not a crash mid-reconcile.
## Out of Scope
- Renaming variables in parser.py (F2, deferred)
## Invariants
- A bad input file never produces a partial reconciliation.
## Acceptance Criteria
- Missing/!numeric `amount` → exit non-zero with a named error; no rows written.
```

## 5. Run an implementation prompt

Hand the slice to an agent with the [slice implementation prompt](../prompts/slice-implementation.prompt.md). Excerpt:

```markdown
## Slice
H1 — Validate CSV columns before reconciling
## Hard scope
Implement only: column validation at load time + tests.
## Out of scope
Do not rename existing variables. Do not touch the reconcile step.
## Required output
Return the implementation report (templates/implementation-report.md).
```

## 6. Receive an implementation report

The agent returns an [implementation report](../templates/implementation-report.md) — **the handoff object** for the slice. Excerpt:

```markdown
# Implementation Report — Slice H1
## Files changed
| `parser.py` | Added validate_columns() before parsing |
| `tests/test_parser.py` | Added missing/non-numeric amount cases |
## Tests run
| `pytest tests/test_parser.py` | PASS |
## Completion status
COMPLETE — bad input now exits cleanly; no partial writes.
```

## 7. Update the phase tracker

The [phase tracker](../templates/phase-tracker.md) is **the control surface** — scope, evidence, and gate readiness live here, updated from the report:

```markdown
| # | Slice | Status | Commit | Notes |
| H1 | Validate CSV columns | 🟢 | abc1234 | Tests pass; F2 deferred |
```

## 8. Make a gate / closeout decision

At the phase boundary, run the [phase closeout prompt](../prompts/phase-closeout.prompt.md). Closeout records **completed work, validation, deferred items, and next options**:

```markdown
# Phase Closeout — HARDEN
## Decision
PROCEED
## Completed (with evidence)
- H1 — column validation; `pytest` PASS; commit abc1234
## Deferred / Carry-Forward
- F2 — naming cleanup in parser.py (maintainability; not a blocker)
## Open Blockers
- None
```

Unknown is not pass: a slice with no evidence would block the decision rather than being assumed complete.

## 9. Decide next options

From the closeout, the choices are explicit:

- **Proceed** to the next phase (e.g. OPERATE — logging/observability).
- **Pick up deferred work** (F2) only if it becomes cheap and adjacent.
- **Close out** to production if all blockers are resolved and accepted risks are signed off.

---

## The loop, in one line

Input → classify → plan → slice → implement → **report** → **tracker** → gate → next. The report is the handoff; the tracker is the control surface; the gate decides on evidence, not optimism.
