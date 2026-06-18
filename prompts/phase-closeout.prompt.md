# Close Out a Phase

You are deciding whether a phase can be closed, based on evidence.

Do not modify code. Do not start new work. Unknown is not pass.

## Inputs

- Phase / gate being closed: `<phase>`
- Tracker state: `<path-or-link>`
- Implementation reports: `<paths-or-links>`
- Test results: `<path-or-link or pasted>`
- Manual validation notes: `<path-or-link>`
- Deferred / carry-forward work: `<path-or-link>`

## Goal

Produce an evidence-based decision on whether this phase is complete, and what carries forward.

## Instructions

1. List every slice in the phase and its status from the tracker.
2. For each completion blocker, confirm it is resolved with evidence (test, report, manual check). If evidence is missing, mark it UNKNOWN — not pass.
3. Confirm the phase invariants still hold.
4. Separate:
   - resolved items (with evidence)
   - accepted risks (named and signed off)
   - deferred work (recorded in carry-forward)
   - open blockers (must be resolved before close)
5. For POC-to-production CLOSEOUT, confirm every production blocker is resolved and every accepted risk is explicitly signed off.
6. Make a clear decision. Do not let process-visibility gaps and technical blockers be confused for each other.

## Required Output Format

Return exactly:

# Phase Closeout — <Phase>

## Decision

PROCEED / DO NOT PROCEED / PROCEED WITH ACCEPTED RISKS

## Slice Status

| Slice | Status | Evidence |
|---|---|---|

## Completion Blockers

| Blocker | Status | Evidence | Action |
|---|---|---|---|

## Invariants Confirmed

## Accepted Risks

## Deferred / Carry-Forward

## Open Blockers

## Rationale

<short evidence-based justification for the decision>
