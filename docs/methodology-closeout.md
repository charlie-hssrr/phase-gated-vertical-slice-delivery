# Methodology Update — Closeout Note

This note closes out the current methodology update. It records what was added, which workflows are now recommended, and what was deliberately left out.

For the methodology itself, start at [docs/methodology.md](methodology.md).

---

## Summary of what was added

This update extended the core Phase A / B / C methodology with a hardening mode and an AI-assisted delivery loop:

- **[POC-to-production hardening mode](poc-to-production-mode.md)** — applies the phase/slice/gate discipline to an existing POC, with a hardening phase set (REVIEW, HARDEN, OPERATE, CLOSEOUT) and a step that classifies review findings before any become work.
- **[Lightweight engineer guide](lightweight-engineer-guide.md)** — the per-slice loop to follow.
- **[Implementation report template](../templates/implementation-report.md)** — the standard handoff object returned at the end of every slice.
- **[Repo review to phase plan template](../templates/repo-review-to-phase-plan.md)** — converts classified findings into a hardening plan.
- **AI implementation, review, and closeout prompts** — [slice implementation](../prompts/slice-implementation.prompt.md), [implementation review](../prompts/implementation-review.prompt.md), [phase closeout](../prompts/phase-closeout.prompt.md).
- **[POC-to-production repo review prompt](../prompts/poc-to-production-repo-review.prompt.md)** — the REVIEW1 entry point for an existing repo.
- **[Worked examples](../examples/)** — a [spec-to-closeout walkthrough](../examples/spec-to-closeout-walkthrough.md) of the full loop, plus a [repo review](../examples/poc-to-production-review-example.md) and the [hardening plan](../examples/poc-to-production-hardening-plan-example.md) it converts into.

## Current recommended workflows

| Situation | Use |
|---|---|
| New or ambiguous build | [Phase A / B / C methodology](methodology.md) |
| Existing POC needing production hardening | [POC-to-production mode](poc-to-production-mode.md) |
| AI-assisted implementation | [Slice prompt](../prompts/slice-implementation.prompt.md) + [implementation report](../templates/implementation-report.md) |
| Reviewing a repo before hardening | [Repo review prompt](../prompts/poc-to-production-repo-review.prompt.md) → [plan template](../templates/repo-review-to-phase-plan.md) |
| Closing a phase | [Phase closeout prompt](../prompts/phase-closeout.prompt.md) |

For hardening, the recommended sequence is REVIEW1 → PLAN1 → HARDEN1+ → CLOSEOUT, described in [POC-to-production mode](poc-to-production-mode.md#recommended-sequence).

## Core safety principles

These hold across every workflow:

- A finding is an observation, not a ticket — classify before converting to work.
- Prioritise production risk over architectural purity; not every imperfection is a blocker.
- Keep every slice narrow and reviewable; do not bundle unrelated changes.
- State non-goals explicitly to prevent scope drift.
- Enforce invariants in the correct layer, not only in the UI.
- Gates are evidence-based — unknown is not pass; deferred work must be named, not lost.
- The human reviewer owns completion; the AI agent does not.

## The prompt / report loop

AI-assisted delivery runs as a closed loop per slice:

1. Scope the slice with the [slice implementation prompt](../prompts/slice-implementation.prompt.md).
2. The agent implements narrowly and returns an [implementation report](../templates/implementation-report.md).
3. The human reviews the diff and report, optionally using the [implementation review prompt](../prompts/implementation-review.prompt.md).
4. At the phase boundary, the [phase closeout prompt](../prompts/phase-closeout.prompt.md) drives the go / no-go decision.

The implementation report is the handoff object that keeps the loop auditable.

## What is intentionally not included

- No automation, CI, or GitHub Actions — this is a documentation methodology, not tooling.
- No project- or company-specific content; examples are fictional and generic.
- No prescribed language, framework, or test runner.
- No replacement for the core A / B / C model — the hardening mode extends it.
- No ticketing or project-management integration.

## Known future improvements

- An optional navigation index ([docs/index.md](index.md)) if the doc set keeps growing.
- More worked examples covering security-sensitive and infrastructure-heavy slices.
- Guidance on automating invariant checks once a project has stable coverage.

## Readiness statement

The methodology repo is ready to use for:

- new phase-gated builds
- POC-to-production hardening plans
- AI-assisted vertical slice implementation
- repo review to slice-plan conversion
