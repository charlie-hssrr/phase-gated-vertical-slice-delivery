# Review a Completed Slice Implementation

You are reviewing a completed slice against its spec and the implementation report.

Do not modify code unless explicitly asked. You own whether the slice is complete; the implementer does not.

## Inputs

- Slice spec: `<path-or-link>`
- Implementation report: `<path-or-link>`
- Diff summary: `<path-or-link or pasted>`
- Test output: `<path-or-link or pasted>`
- Tracker updates: `<path-or-link>`

## Goal

Determine whether the implementation matches the slice scope, preserves the safety invariants, and has enough evidence to be considered complete.

## Check

1. **Scope match**
   - Was only the approved scope implemented?
   - Was any out-of-scope or unrelated cleanup introduced?
2. **Invariants**
   - Does every safety invariant in the spec still hold?
   - Is any invariant enforced only in the frontend when it should be backend-enforced?
3. **Behaviour**
   - Does the diff actually solve the stated problem?
   - Are the review findings it claims to address genuinely addressed?
4. **Tests**
   - Do tests validate behaviour, not implementation detail?
   - Would at least one test fail if the behaviour silently regressed?
   - Are negative / boundary cases covered where relevant?
5. **Evidence**
   - Does the test output confirm the claimed results?
   - Are "no callers" / "unused" claims verified?
6. **Risk**
   - Any hidden migration, compatibility, or operational risk?
   - Any unverified assumption in the report?

## Required Output Format

Return exactly:

# Slice Review Summary

## Decision

PASS / PASS WITH RISKS / FAIL

## Scope Match

## Invariants Review

## Behaviour & Findings Addressed

## Tests Review

## Issues Found

| Severity | Issue | Why It Matters | Required Action |
|---|---|---|---|

## Evidence Confirmed

## Remaining Risks

## Recommended Tracker Updates

## Recommended Next Step
