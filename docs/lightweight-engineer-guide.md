# Lightweight Engineer Guide

A short, practical guide for delivering one slice at a time.

You can follow this without reading the full methodology. It assumes a slice has already been scoped and a tracker exists. If you need the full model, see the [methodology](methodology.md).

This guide applies to both human engineers and AI agents. The output of every slice is an [implementation report](../templates/implementation-report.md).

---

## Core slice loop

Run this loop for every slice, in order:

1. **Read the tracker.** Know the current phase, what is done, and where this slice fits.
2. **Read the slice spec.** Understand the goal, scope, out-of-scope, and invariants.
3. **Inspect the repo before changing code.** Find the existing patterns, callers, and tests. Do not start from scratch.
4. **Implement only the slice.** Make the smallest change that satisfies the spec.
5. **Add or update tests.** Cover the behavior this slice changes. Tests ship in the same change.
6. **Run validation.** Run the tests and any required checks. Confirm they pass.
7. **Update docs/tracker if required.** Record decisions, deferred work, and slice status.
8. **Return the implementation report.** Hand off using the report template.

---

## Slice rules

- One slice solves one defined problem.
- Inspect before patching; reuse existing patterns.
- Keep the diff small enough to review end-to-end.
- Tests for behavior-changing slices ship in the same change.
- Preserve every invariant listed in the spec.
- Record any decision where you deviated from the spec or chose between options.
- Verify claims like "no callers" or "unused" with an actual search.

## What not to do

- Do not broaden scope beyond the slice spec.
- Do not perform opportunistic cleanup because the code is nearby.
- Do not rename or refactor unrelated code.
- Do not introduce speculative abstractions for future needs.
- Do not weaken a safety or ownership invariant to make the change easier.
- Do not treat "it compiles" or "it runs" as validation.
- Do not perform git operations unless explicitly asked.
- Do not claim completion without evidence.

## Completion rule

A slice is complete when there is **evidence**, not when code is written.

Minimum evidence: the scope is implemented, tests pass, invariants are confirmed, and the implementation report is filled in. If a behavior change cannot be validated, the slice is not complete.

## Engineer / AI handoff rule

The implementer (human or AI) implements and reports. The reviewer owns whether the slice is complete.

- The implementer never self-certifies completion as final.
- The reviewer reads the diff, checks invariants, and confirms evidence.
- Handoff in both directions happens through the implementation report.

## The implementation report is the handoff object

Every slice ends with an [implementation report](../templates/implementation-report.md).

It is the single artifact that moves work from implementer to reviewer, and from one slice to the next. If the report is incomplete, the handoff is incomplete.
