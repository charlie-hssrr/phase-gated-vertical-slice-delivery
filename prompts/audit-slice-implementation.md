# Audit Slice Implementation

You are reviewing an implementation against an approved slice spec.

Do not modify code unless explicitly asked.

## Goal

Determine whether the implementation matches the slice scope, preserves invariants, and has enough evidence to be considered complete.

## Review Against

- Approved slice spec
- Phase tracker
- Project invariants
- Tests and validation evidence
- Actual diff

## Check

1. Scope control
   - Was only the approved scope implemented?
   - Was unrelated cleanup introduced?

2. Invariants
   - Were security, ownership, data, or compatibility rules preserved?
   - Are any invariants enforced only in the frontend when they should be backend-enforced?

3. Tests
   - Do tests validate behaviour?
   - Would at least one test fail if the expected behaviour silently changed?
   - Are negative cases covered where relevant?

4. API / contract changes
   - Were callsites updated?
   - Is compatibility preserved or intentionally broken?
   - Are defaults safe?

5. Risk
   - Any hidden migration risk?
   - Any operational dependency?
   - Any unverified assumption?

## Required Output Format

Return exactly:

# Slice Audit Summary

## Decision

PASS / PASS WITH RISKS / FAIL

## Scope Match

## Invariants Review

## Tests Review

## Files Reviewed

## Issues Found

| Severity | Issue | Why It Matters | Required Action |
|---|---|---|---|

## Evidence

## Remaining Risks

## Recommended Tracker Updates

## Recommended Next Step