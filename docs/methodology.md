# Phase-Gated Vertical Slice Delivery

> **Purpose:** turn ambiguous technical specs into controlled, reviewable, production-grade systems — by building in phases, delivering in narrow vertical slices, and gating progress on evidence rather than optimism.

## Choose your workflow

| Situation | Use |
|---|---|
| New or ambiguous build | This methodology — Phase A / B / C (below) |
| Existing POC needing production hardening | [POC-to-production mode](poc-to-production-mode.md) |
| AI-assisted implementation | [Slice prompt](../prompts/slice-implementation.prompt.md) + [implementation report](../templates/implementation-report.md) |
| Reviewing a repo before hardening | [POC-to-production repo review prompt](../prompts/poc-to-production-repo-review.prompt.md) |
| Closing a phase | [Phase closeout prompt](../prompts/phase-closeout.prompt.md) |

> In a hurry? [The method on a page](method-on-a-page.md) is the whole thing in one screen.

## Related guides

- [The method on a page](method-on-a-page.md)
- [POC-to-production hardening mode](poc-to-production-mode.md)
- [Lightweight engineer guide](lightweight-engineer-guide.md)
- [Implementation report template](../templates/implementation-report.md)
- [Repo review prompt](../prompts/poc-to-production-repo-review.prompt.md)
- [Worked examples](../examples/)
- [Methodology closeout note](methodology-closeout.md)

## Contents

- [Introduction](#introduction)
- [1. Core Philosophy](#1-core-philosophy)
- [2. Phase Model](#2-phase-model)
- [3. Slice Model](#3-slice-model)
- [4. Tracker Model](#4-tracker-model)
- [5. Gate Model](#5-gate-model)
- [6. Decision Logging](#6-decision-logging)
- [7. Lessons / Carry-Forward](#7-lessons--carry-forward)
- [8. Infrastructure Prerequisites](#8-infrastructure-prerequisites)
- [9. AI-Agent-Assisted Delivery Rules](#9-ai-agent-assisted-delivery-rules)
- [10. Review Model](#10-review-model)
- [11. Invariant Management](#11-invariant-management)
- [12. Spec-to-Slice Breakdown Process](#12-spec-to-slice-breakdown-process)
- [13. Slice Sequencing Heuristics](#13-slice-sequencing-heuristics)
- [14. Completion Evidence](#14-completion-evidence)
- [15. Common Failure Modes](#15-common-failure-modes)
- [16. Reusable Phase Planning Template](#16-reusable-phase-planning-template)
- [17. Recommended Operating Cadence](#17-recommended-operating-cadence)
- [18. Right-Sizing the Method](#18-right-sizing-the-method)
- [19. Final Rule Set](#19-final-rule-set)
- [POC-to-Production Hardening Mode](#poc-to-production-hardening-mode)
- [20. One-Line Summary](#20-one-line-summary)
- [Appendix — Influences and Prior Art](#appendix--influences-and-prior-art)

---

## Introduction

Phase-Gated Vertical Slice Delivery is a way to build complex systems without losing control of them as they grow.

It did not start as a methodology. It was forged over more than two years building an enterprise product platform from the ground up — a multi-tenant system where the architecture kept moving under active development, and where getting the data model, ownership boundaries, and security right mattered as much as shipping features. Sustaining that for that long without a rewrite, and without a slow slide into chaos, took a discipline I eventually wrote down. The methodology is that discipline, generalised.

The thinking then proved itself on other work. Applied to a proof of concept that needed to become production-ready, it meant pausing to harden the foundations after a review surfaced real risks, rather than piling features on a shaky base — that pattern became the [POC-to-production hardening mode](poc-to-production-mode.md). Applied to a ground-up service rebuild, it showed up as an instinct to cut the work into small slices that each ran top-to-bottom through the system and stood on their own — that became the Phase A / B / C model. (I had landed on vertical-slice delivery by accident, before I knew it was an established idea with a name.) The rest of this document is the discipline that made both repeatable.

All of it shared the same conditions: the architecture was still moving while implementation was underway, getting the data model and ownership boundaries right mattered as much as shipping the feature, and an AI coding agent could produce a week of work in an afternoon — and just as quickly wander off the spec. Standard Agile was too loose to hold that line; a big upfront design would have been obsolete before the first slice merged.

None of the underlying ideas are new. The method leans on Vertical Slice Architecture (build outcomes, not layers), Domain-Driven Design (entities, boundaries, invariants), stage-gated delivery (explicit transitions, gated on evidence), small-batch engineering, and secure-systems thinking (no implicit trust, no silent defaults). What it adds is the assembly and the discipline — most of the ceremony stripped out, what remains pointed squarely at execution. The full lineage is in the [appendix](#appendix--influences-and-prior-art).

The aim, in one sentence:

> Turn ambiguous specs into controlled, reviewable, production-grade systems — without relying on large upfront design or uncontrolled iteration.

Everything else follows from five moves: separate *structure*, *product completeness*, and *hardening* into distinct phases; deliver in small, vertically complete slices; name and enforce the system's invariants early; gate progress on evidence rather than optimism; and keep a living record of the decisions and lessons along the way.

It pays off most on platform and infrastructure work, AI-enabled systems, multi-tenant or security-sensitive applications, and anything whose architecture is still finding its shape. On small, low-stakes, easily-reversed work it is usually too much — see [Right-Sizing the Method](#18-right-sizing-the-method).

---

# Phase-and-Slice Delivery Methodology

## Purpose

This methodology defines a practical way to scope, build, track, and govern software delivery using phased execution and vertically sliced implementation.

It is designed for projects where:

- the architecture is still evolving
- implementation is done in small increments
- AI coding agents may assist with implementation
- quality gates matter
- the builder needs a repeatable way to prevent scope drift, silent regressions, and half-complete product flows

The method is intentionally generic. It can be used for platform work, product features, internal tooling, infrastructure, integrations, or data workflows.

## How to read this

This document describes the method at full weight. That does not mean every project needs every artefact.

The ideas — phases, vertical slices, invariants, evidence-based gates — carry most of the value. The templates and trackers are there to serve those ideas, not the other way round. On a small or low-risk piece of work, a slice might be one line of scope and a passing test. On a multi-tenant or security-sensitive system, the full kit earns its cost.

Read the philosophy and the phase/slice/gate model as the core. Treat the paperwork as something you dial up or down. [Right-Sizing the Method](#18-right-sizing-the-method) explains how to choose.

---

# 1. Core Philosophy

## 1.1 Build in phases, not feature piles

A project should not be treated as one large backlog of loosely related tasks.

Instead, split delivery into phases with different responsibilities:

| Phase | Purpose | Primary Question |
|---|---|---|
| Phase A — Foundation | Establish core model, architecture, and invariants | Is the system structurally correct? |
| Phase B — Completion | Close product gaps and make the flow usable end-to-end | Does the system work as a coherent product? |
| Phase C — Hardening | Improve resilience, security, observability, performance, and operational readiness | Can this run safely in production? |

This prevents the common failure mode where a team mixes foundational modelling, product UX, edge-case hardening, and polish in the same implementation pass.

## 1.2 Build slices, not layers

A slice is a small, reviewable unit of delivery that produces one coherent outcome. It will usually cut through several layers at once — data model, backend service, frontend state, UI behaviour, tests, telemetry, documentation — and that is fine. What matters is that it solves a single defined problem, not that it stays inside one layer.

The failures are easy to recognise once you name them. A slice is too horizontal when it is "build all the backend models" with nothing usable at the end. Too broad when it is "implement the whole connector platform". Too vague when it is "improve workspace UX". And too leaky when it starts dragging in unrelated cleanup simply because the code happened to be nearby.

## 1.3 Invariants before features

Every project has rules that must never break — not preferences, not nice-to-haves, but conditions the system is wrong without. Every record has a tenant boundary. Every workspace has a default, usable entry point. Every query enforces ownership scope. Every exported artefact carries its provenance. Every background job is traceable.

These get identified early, reviewed at every slice, and enforced in the layer that actually owns correctness. If an invariant defines whether the system is correct, it belongs in the backend or a shared domain service — never in frontend behaviour alone, where a second UI path can quietly violate it.

## 1.4 Explicit non-goals are a delivery tool

Every phase and every slice says what it is *not* doing — and that sentence does real work.

Ambiguity is what scope expansion feeds on, and nothing fills an ambiguous gap faster than an AI agent or an eager junior engineer. A clear out-of-scope section is the cheapest guardrail you have against speculative abstractions, premature polish, unplanned migrations, cleanup rabbit holes, and broad rewrites smuggled in as small fixes.

---

# 2. Phase Model

## 2.1 Phase A — Foundation

### Goal

Make the system structurally correct.

Phase A establishes the core model, ownership boundaries, contracts, and minimum viable flow. It is not primarily about polished UX or complete product behavior.

### Typical work

- define entities and relationships
- define ownership and tenancy boundaries
- introduce core contracts
- create skeleton services/routes/components
- preserve backward compatibility where required
- add regression tests around existing behavior
- establish critical logs or telemetry
- capture architecture decisions

### Expected outputs

- core entities exist
- minimum happy path works
- critical invariants are enforced or explicitly tracked
- high-risk architectural decisions are recorded
- follow-on slices have a stable baseline

### What Phase A is not

- not a full product completion pass
- not a broad UX polish pass
- not a production-hardening phase
- not the time for speculative future abstractions
- not the time to solve every naming or legacy cleanup issue

### Phase A Definition of Done

Phase A is complete when:

- all foundation slices are implemented and reviewed
- core data model is coherent
- ownership/security boundaries are enforced or explicitly gated
- existing behavior has not regressed
- known deviations are recorded
- Phase B can start without guessing about architecture intent

---

## 2.2 Phase B — Completion

### Goal

Make the system coherent, usable, and demoable end-to-end.

Phase B closes the gap between the intended model and actual product behavior.

### Typical work

- complete missing product loops
- add management surfaces
- add search, filtering, export, or retrieval where needed
- fix UX contradictions
- replace temporary frontend-only conventions with backend-backed contracts
- simplify confusing flows
- complete key acceptance criteria from the original spec

### Expected outputs

- users can complete the main workflow without manual setup
- obvious product contradictions are removed
- core flows are demo-ready
- product surfaces feel intentional rather than scaffolded
- remaining gaps are known and deliberately deferred

### What Phase B is not

- not a full hardening pass
- not a broad redesign
- not a dumping ground for every nice-to-have feature
- not the right time for major unrelated architecture rewrites

### Phase B Definition of Done

Phase B is complete when:

- the primary workflow works end-to-end
- the product can be demoed without explaining internal gaps
- core management actions exist
- user-visible states are clear
- major spec-vs-reality gaps are closed
- Phase C can focus on hardening rather than missing product behavior

---

## 2.3 Phase C — Hardening

### Goal

Make the system reliable, observable, secure, maintainable, and ready for production use.

Phase C assumes the main product flow exists and focuses on making it safe to operate.

### Typical work

- tighten security checks
- improve failure handling
- add observability and metrics
- refine performance and latency
- harden configuration and environment handling
- remove temporary compatibility paths
- validate operational runbooks
- improve test coverage for edge cases
- clean up naming drift where now justified

### Expected outputs

- predictable failure modes
- useful logs and metrics
- no known critical security gaps
- stable configuration
- documented operational dependencies
- production readiness checklist passed

### What Phase C is not

- not the time to discover the core product still does not work
- not the time to introduce major new product concepts
- not the time for unbounded refactoring

### Phase C Definition of Done

Phase C is complete when:

- core flows are reliable under expected conditions
- failure cases are handled deliberately
- operational prerequisites are documented
- monitoring and debugging paths exist
- release or production gate is passed

---

## 2.4 Phases are ordered, not one-way

The phase order is a default, not a prohibition on going back.

Real projects discover in Phase B that a Phase A assumption was wrong, or find in Phase C that a product loop was never finished. That is normal. The gate exists to stop you from moving *forward* on optimism — it does not forbid moving *back* on evidence.

When later work reveals a foundation gap:

- treat it as a new foundation slice (for example, a late `A` slice), not a quiet patch inside the current slice
- record the reason in the decision log so the regression in phase is intentional and visible
- re-check the affected gate rather than assuming it still holds

The failure mode to avoid is not revisiting an earlier phase. It is revisiting it *silently* — folding foundational rework into an unrelated slice where no one reviews it as foundational.

---

# 3. Slice Model

## 3.1 What a slice is

A slice is a discrete implementation unit with:

- one clear outcome
- bounded scope
- explicit non-goals
- testable acceptance criteria
- reviewable diff size
- clear rollback or recovery path where relevant

A slice should be small enough to review properly but large enough to produce a meaningful system change.

## 3.2 Slice naming

Use phase-prefixed IDs:

```text
A1, A2, A3...
B1, B2, B3...
C1, C2, C3...
```

Where useful, split large slices:

```text
A1-backend
A1-frontend
A6a-shadow-mode
A6b-routing-integration
```

Use sub-slices when the work has different blast radius or validation requirements.

## 3.3 Slice template

```markdown
# Slice <ID> — <Title>

## Goal
What this slice unlocks.

## Current Problem
What is missing, broken, risky, or incoherent today.

## Scope
- Backend:
- Frontend:
- Data model:
- Tests:
- Documentation:

## Out of Scope
- Explicit exclusions
- Related work intentionally deferred

## Invariants
- Rules that must remain true after this slice

## Acceptance Criteria
- Observable behavior 1
- Observable behavior 2
- Regression expectation
- Security/ownership expectation if relevant

## Likely Files / Areas
- Paths or modules expected to be touched

## Validation Plan
- Unit tests
- Integration tests
- Manual checks
- Logs/telemetry checks

## Risks
- Known uncertainty
- Migration risk
- Compatibility risk
- Operational dependency
```

---

# 4. Tracker Model

Every phase should have a tracker.

The tracker is not just a task list. It is the control surface for scope, evidence, decisions, and gate readiness.

## 4.1 Tracker sections

A strong tracker includes:

1. Phase metadata
2. Status legend
3. Slice tracker
4. Per-slice completion checklist
5. Phase invariants
6. Decisions log
7. Lessons / carry-forward
8. Infrastructure prerequisites
9. Phase gate
10. Manual validation log
11. Final readiness decision

---

## 4.2 Phase tracker template

```markdown
# Phase <X> — Progression Checklist & Status Tracker

**Branch:** `<branch-name>`
**Spec:** `<path-or-link-to-spec>`
**Started:** `<date>`
**Target complete:** `<date or TBD>`

---

## Status Legend

- 🔴 Not started
- 🟡 In progress
- 🟢 Complete / merged
- ⚠️ Blocked / needs attention
- ⏸ Deferred

---

## Slice Tracker

| # | Slice | Status | Commit / PR | Merged | Notes |
|---|---|---|---|---|---|
| A1 | <slice title> | 🔴 | — | — | — |
| A2 | <slice title> | 🔴 | — | — | — |

---

## Per-Slice Completion Checklist

### Pre-implementation
- [ ] Scope proposal requested before implementation
- [ ] Scope reviewed against spec
- [ ] Non-goals confirmed
- [ ] Design questions answered
- [ ] Risk level assigned

### Implementation
- [ ] Implementation matches approved scope
- [ ] Tests included in same change
- [ ] New helpers/modules explicitly justified
- [ ] Callsite enumeration included for API changes
- [ ] No unrelated cleanup included

### Review
- [ ] Diff read end-to-end
- [ ] Tests verify behavior, not implementation detail
- [ ] At least one test would fail if default behavior silently changed
- [ ] Critical invariants preserved
- [ ] Regression path tested
- [ ] Rollback/recovery path documented where relevant
- [ ] Claimed “no callers” or “no usage” statements verified with search

### Post-merge / Post-completion
- [ ] Tracker updated with commit/PR
- [ ] Decisions recorded
- [ ] Lessons captured
- [ ] Deferred work moved to carry-forward section
- [ ] Next slice starts from clean baseline
```

---

# 5. Gate Model

A gate is a decision point between phases or high-risk streams.

It prevents the team from starting the next phase based on optimism.

## 5.1 Gate categories

Use three classes of gate items:

| Category | Meaning |
|---|---|
| Hard blocker | Next phase cannot start until resolved |
| Completion blocker | Next phase can start, but cannot be considered complete until resolved |
| Deferred / accepted risk | Known issue deliberately carried forward |

## 5.2 Gate tracker template

```markdown
# Phase <X> → Phase <Y> Gate Tracker

## Gate Policy

- This tracker gates: <what exactly is being gated>
- Hard blockers: <types of issues that block start>
- Not a start blocker: <explicit non-blockers>
- Moved to completion gate: <items deferred but still required later>

---

## Start Blockers

- <None currently identified / list blockers>

---

## Completion Blockers

- <items that do not block start but must be completed before phase close>

---

## Deferred From Previous Phase

- <deferred item and reason>

---

## Gate Status Table

| Gate Item | Status | Evidence | Notes / Action |
|---|---|---|---|
| <item> | PASS / FAIL / MOVED / UNKNOWN | <test, commit, manual check, doc> | <action> |

---

## Required Checks

- <test command or validation step>
- <manual behavior check>
- <security/logging check>
- <architecture decision check>

---

## Manual Validation Log

| Check | Status | Evidence | Date | Notes |
|---|---|---|---|---|
| <check> | PASS | <evidence> | <date> | <notes> |

---

## Final Readiness Decision

<Clear decision: proceed / do not proceed / proceed with accepted risks>

Reason:
<short rationale based on evidence>
```

## 5.3 Gate rules

- A gate must be evidence-based.
- Unknown is not pass.
- Deferred is allowed only if explicitly named.
- Start blockers and completion blockers must be separated.
- Do not allow process visibility issues to be confused with technical blockers.
- Do not allow technical blockers to be hidden as process issues.

---

# 6. Decision Logging

Decision logs are essential when building in slices.

Most important decisions happen mid-slice, not in the original spec.

Without a decision log, future reviewers will not know whether something was intentional, accidental, or unfinished.

## 6.1 Decision log template

```markdown
## Decisions Log

| Slice | Decision | Rationale | Spec Implication |
|---|---|---|---|
| A1 | <decision> | <why> | <none / clarification / deviation> |
```

## 6.2 What belongs in the decisions log

Record decisions when:

- implementation differs from the original spec
- the team chooses one design path over another
- a safety or security behavior is clarified
- a default is rejected or introduced
- a migration is deferred
- a helper/module/package is added outside the original scope list
- a future forcing function is intentionally added

## 6.3 What does not belong

Do not log every small implementation detail.

The decision log is for future understanding, not noise.

---

# 7. Lessons / Carry-Forward

The lessons section captures patterns discovered during execution.

This is especially useful for solo builders, technical leads, or teams using AI coding agents.

## 7.1 Lesson template

```markdown
## Lessons / Carry-Forward

- <lesson learned>
- <practice to repeat>
- <risk to watch in later slices>
- <thing to add to future prompts>
```

## 7.2 Examples of useful lessons

- Explicit out-of-scope instructions reduce implementation drift.
- Compile-only checks are not validation.
- Tests should prove behavior, not method calls.
- Security-sensitive defaults should usually be explicit, not implicit.
- A slice with no runtime consumer may not need manual end-to-end validation yet.
- Claims like “no callers” must be verified with search.
- If two reviewers disagree, prefer the argument grounded in system purpose over the one grounded only in local documentation.

---

# 8. Infrastructure Prerequisites

Some slices depend on infrastructure that application code does not create.

Track these explicitly.

## 8.1 Template

```markdown
## Infrastructure Prerequisites

| Prerequisite | Required By | Details | Status |
|---|---|---|---|
| <resource> | <slice> | <configuration> | <status> |
```

## 8.2 Rule

Infrastructure dependencies must be flagged when discovered, not left for deployment time.

Examples:

- database container required
- queue required
- secret required
- role assignment required
- environment variable required
- storage container required
- index field required

---

# 9. AI-Agent-Assisted Delivery Rules

This methodology works especially well with AI coding agents, but only if the human keeps review ownership.

## 9.1 Recommended agent workflow

1. Human writes or approves the slice scope.
2. Agent audits current implementation before patching.
3. Agent proposes patch plan.
4. Human reviews plan against scope.
5. Agent implements narrowly.
6. Agent reports files changed, tests run, and risks.
7. Human reviews the diff end-to-end.
8. Human runs key tests locally.
9. Human updates tracker and commits.

## 9.2 Agent instruction principles

Tell the agent:

- inspect before patching
- do not start from scratch
- preserve existing patterns
- avoid speculative abstractions
- list any functions modified beyond expected scope
- include tests in the same change
- report unverified assumptions
- do not claim completion without evidence
- do not handle git unless explicitly requested

## 9.3 Human responsibilities

The human reviewer owns:

- final scope control
- diff review
- test execution gate
- merge decision
- tracker updates
- decision log
- acceptance of deferred risks

AI can implement. It should not be the authority on whether the slice is complete.

---

# 10. Review Model

## 10.1 Pre-implementation review

Before implementation starts, confirm:

- the slice has one clear outcome
- the scope is small enough
- non-goals are explicit
- invariants are known
- validation is possible
- dependencies are understood

## 10.2 Implementation review

During review, check:

- did the diff solve the stated problem?
- did it introduce unrelated changes?
- did it weaken any invariant?
- are new defaults safe?
- are callsites updated deliberately?
- do tests cover behavior?
- are failure paths handled?
- are logs/telemetry preserved where required?

## 10.3 Security-critical review

For security-sensitive slices, add extra review:

- adversarial review pass
- scope-boundary tests
- negative tests
- default-value audit
- log/audit trail verification
- manual regression path

A security-critical slice may justify two review rounds.

## 10.4 Product-coherence review

For product-facing slices, check:

- can a user complete the intended flow?
- is there any state where the product contradicts itself?
- is the behavior backend-backed or frontend-assumed?
- does the UI reflect actual system capability?
- are empty/loading/error states coherent?

---

# 11. Invariant Management

## 11.1 Invariant template

```markdown
## Spec Invariants

Every slice review must confirm:

- [ ] <invariant 1>
- [ ] <invariant 2>
- [ ] <invariant 3>
```

## 11.2 Invariant examples

- Every query enforces tenant ownership.
- Every workspace has a default usable entry point.
- Existing document retrieval remains unchanged unless explicitly modified.
- Raw provider results never leak past the normalized result contract.
- Backward compatibility fields remain populated until the migration phase.
- Security logs are emitted on every sensitive retrieval path.

## 11.3 Rule

If an invariant is important enough to list, it should eventually have automated coverage.

Manual checks are acceptable early, but they should not remain the only line of defense for critical behavior.

---

# 12. Spec-to-Slice Breakdown Process

Use this process to convert a raw spec into a phase/slice delivery plan.

## Step 1 — Extract the model

Identify:

- entities
- relationships
- ownership boundaries
- lifecycle states
- critical identifiers
- external systems

## Step 2 — Extract invariants

Ask:

- what must always be true?
- what would create a security issue if false?
- what would create a product contradiction if false?
- what must remain backward compatible?

## Step 3 — Separate phases

Classify work into:

- Foundation: model, contracts, skeletons, invariants
- Completion: usable flows, management, retrieval, UX coherence
- Hardening: scale, security depth, failure handling, observability

## Step 4 — Define slices

For each phase:

- group work into narrow outcomes
- avoid broad horizontal layers
- isolate high-risk work
- split frontend/backend if different blast radius
- sequence prerequisites first

## Step 5 — Add gates

Define:

- phase start blockers
- phase completion blockers
- invariants to verify
- manual validation requirements
- decisions that must be recorded

## Step 6 — Add tracker

Create the phase tracker before implementation starts.

The tracker should evolve as the project is built.

---

# 13. Slice Sequencing Heuristics

Use these rules when deciding order.

## 13.1 Build order

Prefer this order:

1. contracts and model
2. security/ownership boundaries
3. compatibility wrappers
4. skeleton integration
5. routing or orchestration
6. product surface
7. management controls
8. observability and hardening
9. cleanup

## 13.2 Split when risk changes

Split a slice if one part has much higher blast radius.

Example:

- classifier shadow mode
- classifier-gated routing

These should be separate because one observes and the other changes behavior.

## 13.3 Defer polish until the product loop exists

Do not spend Phase A effort making something elegant if the system model is still moving.

## 13.4 Do not defer tests for behavior-changing slices

If a slice changes behavior, tests belong in that slice.

---

# 14. Completion Evidence

A slice is not complete because code was written.

A slice is complete when there is evidence.

## 14.1 Evidence types

- passing unit tests
- passing regression tests
- manual scenario validation
- log output verification
- diff review
- search/grep verification
- commit/PR reference
- recorded decision
- updated tracker

## 14.2 Minimum evidence by slice type

| Slice Type | Minimum Evidence |
|---|---|
| Pure model / helper | unit tests + diff review |
| Behavior change | unit tests + regression test + manual scenario if applicable |
| Security/scope change | negative tests + boundary tests + log verification |
| UI-only change | visual/manual validation + state handling check |
| API contract change | callsite enumeration + compatibility test |
| Infrastructure-dependent change | prerequisite recorded + environment validation path |

---

# 15. Common Failure Modes

## 15.1 Starting Phase B before Phase A is coherent

Symptom:

- features built on unstable concepts
- repeated rework
- unclear ownership model

Fix:

- enforce Phase A gate
- record unresolved architecture decisions

## 15.2 Treating frontend behavior as a system invariant

Symptom:

- one UI path works, another creates invalid state

Fix:

- move invariant into backend or shared domain service

## 15.3 Accepting compile success as validation

Symptom:

- code compiles but product behavior regresses

Fix:

- require behavior tests and manual checks for behavior-changing slices

## 15.4 Allowing broad cleanup inside narrow slices

Symptom:

- hard-to-review diffs
- hidden regressions
- slice takes longer than expected

Fix:

- log cleanup as carry-forward unless directly required

## 15.5 Unclear deferred work

Symptom:

- known issues vanish from the plan
- next phase starts with hidden assumptions

Fix:

- move deferred work into gate tracker or carry-forward section

---

# 16. Reusable Phase Planning Template

```markdown
# Project Delivery Plan — <Project Name>

## Objective

<What the project is meant to achieve.>

## Current State

<What exists today.>

## Target State

<What should be true when complete.>

## Core Invariants

- <Invariant 1>
- <Invariant 2>
- <Invariant 3>

---

# Phase A — Foundation

## Goal

<Structural correctness goal.>

## Slices

| Slice | Title | Outcome | Risk |
|---|---|---|---|
| A1 | <title> | <outcome> | Low / Med / High |

## Phase A Gate

- [ ] <gate item>
- [ ] <gate item>

---

# Phase B — Completion

## Goal

<Product coherence goal.>

## Slices

| Slice | Title | Outcome | Risk |
|---|---|---|---|
| B1 | <title> | <outcome> | Low / Med / High |

## Phase B Gate

- [ ] <gate item>
- [ ] <gate item>

---

# Phase C — Hardening

## Goal

<Production readiness goal.>

## Slices

| Slice | Title | Outcome | Risk |
|---|---|---|---|
| C1 | <title> | <outcome> | Low / Med / High |

## Production Gate

- [ ] <gate item>
- [ ] <gate item>
```

---

# 17. Recommended Operating Cadence

## Before each slice

- review scope
- confirm non-goals
- confirm tests needed
- identify risk level

## During each slice

- keep changes narrow
- record unexpected decisions
- avoid opportunistic cleanup

## After each slice

- run checks
- review diff
- update tracker
- record lessons
- decide whether next slice can start

## At phase boundary

- create gate tracker
- classify blockers
- record evidence
- decide explicitly whether to proceed

---

# 18. Right-Sizing the Method

The most common way a process like this fails is not that people ignore it. It is that they apply all of it to work that did not need all of it, the overhead outweighs the value, and the method gets abandoned as bureaucracy.

The phases, slices, invariants, and gates are the parts that scale across project sizes. The artefacts are the parts you dial up and down.

## 18.1 The minimum viable loop

The smallest honest version of this method is still recognisable:

1. State the one outcome and what is out of scope.
2. Make the change narrowly.
3. Show evidence it works (a test, a run, a check).
4. Review the diff before it lands.

If you only ever do that, you have kept the spine: one outcome, explicit non-goals, evidence, human review. Everything else is added when the risk justifies it.

## 18.2 Tier artefacts by risk, not by habit

Decide the artefacts per slice from its blast radius, not from a fixed checklist applied to everything.

| Slice risk | Examples | Minimum artefacts |
|---|---|---|
| Low | copy change, isolated helper, additive UI state, low-traffic internal tool | scope line + non-goals, test or manual check, diff review |
| Standard | new behaviour, a new product loop, a contract used in one place | slice spec, tests in the same change, decision log entry if a non-obvious choice was made, diff review |
| High | security or ownership boundary, multi-tenant data path, API contract with multiple callers, migration, anything hard to roll back | full slice spec, negative + boundary tests, callsite enumeration, decision log, gate entry, manual validation log |

The rule is simple: **the artefact has to buy down a real risk on this slice.** If it does not, skip it and say you skipped it.

## 18.3 When to use less

Lean the method down when:

- the project is small, short-lived, or low-stakes
- there is one builder and a fast feedback loop
- the cost of being wrong is low and easily reversible
- you are exploring or prototyping and the model is meant to move

In those cases, keep the philosophy and the minimum viable loop, and drop most of the trackers.

## 18.4 When to use the full weight

Use the full method when the cost of a silent mistake is high:

- multi-tenant or security-sensitive systems
- shared platforms and contracts other teams depend on
- data migrations and anything with a hard-to-reverse blast radius
- work handed between people, or driven by AI agents over many slices
- regulated or audited environments where the decision trail itself is a deliverable

The heavier the consequences and the more hands involved, the more the trackers, gates, and decision logs earn their cost.

## 18.5 Right-sizing is itself a decision

Choosing to run light is a legitimate choice — but it is a decision, not an accident. Record it. "This stream runs at the light tier because it is reversible and single-owner" is one sentence, and it stops the next person assuming corners were cut by mistake.

---

# 19. Final Rule Set

Use these rules as the short version of the methodology.

1. Build foundations before product completion.
2. Build product completion before hardening.
3. Slice vertically around outcomes.
4. Keep every slice narrow and reviewable.
5. State non-goals aggressively.
6. Enforce invariants in the correct layer.
7. Track decisions as they happen.
8. Use gates to separate optimism from evidence.
9. Human reviewer owns completion, not the AI agent.
10. Do not move forward with unknowns disguised as passes.

---

# POC-to-Production Hardening Mode

The Phase A / Phase B / Phase C structure above remains the default. Use it for new or ambiguous builds, where the model, product, and hardening concerns are all still in motion.

When a project **already exists as a working POC** and a repo review has produced findings to act on, use **POC-to-production hardening mode** instead. It applies the same phase/slice/gate/invariant discipline to existing code, with a phase set tuned for hardening (REVIEW, HARDEN, OPERATE, CLOSEOUT) and a step that classifies review findings before any become work.

Use this mode when there is a working system and findings to turn into controlled hardening slices. Use the standard A/B/C model when the foundation is not yet stable.

See: [POC-to-Production Hardening Mode](poc-to-production-mode.md) and the [Lightweight Engineer Guide](lightweight-engineer-guide.md).

---

# 20. One-Line Summary

This methodology is a phase-gated, slice-driven delivery system for turning ambiguous technical specs into controlled, reviewable, production-grade implementation.

---

# Appendix — Influences and Prior Art

This method is a synthesis, not an invention from nothing. It pulls together ideas that have been around for decades and points them at one specific problem: keeping software delivery controlled when implementation moves fast — especially when an AI agent is doing the typing.

Naming the lineage is deliberate. It tells you where to read deeper, and it makes clear which parts are borrowed and which part is the contribution here.

## What it borrows from

| Source | Idea borrowed |
|---|---|
| Stage-Gate (Robert Cooper) | Explicit phases separated by evidence-based go / no-go gates. The "phase-gated" half of the name. |
| Vertical Slice Architecture (Jimmy Bogard) | Build outcomes that cut through every layer, not horizontal layers. The "vertical slice" half of the name. |
| Walking Skeleton / Tracer Bullets (Alistair Cockburn; Hunt & Thomas) | A minimal end-to-end path first, then flesh it out. Phase A is essentially a walking skeleton. |
| Domain-Driven Design (Eric Evans) | Entities, ownership boundaries, and invariants as first-class design concerns. |
| Design by Contract (Bertrand Meyer) | Invariants as rules that must hold, enforced in the right layer. |
| Architecture Decision Records (Michael Nygard) | The decision log — capturing why a choice was made, when it was made. |
| "Make it work, make it right, make it fast" (Kent Beck) | The shape of A → B → C: correctness, then completeness, then hardening. |
| Shape Up (Basecamp / Ryan Singer) | Fixed appetite, aggressive scope-hammering, explicit "no-gos". The closest philosophical sibling — see the contrast below. |
| Lean / small-batch manufacturing | Frequent, small, controlled increments over large batches. |
| Secure-by-design / threat modelling | No implicit trust, no silent defaults, negative and boundary tests on sensitive paths. |

On the AI side, it sits alongside current practice in agentic coding — small reversible steps, human-in-the-loop verification, and not trusting an agent's self-reported "done" — and the broader move toward spec-driven development.

## Where it diverges from Shape Up

Shape Up is the nearest relative, so the difference is worth stating. Shape Up deliberately *avoids* heavy tracking and gates; it bets on a fixed appetite and trusts a small team to manage scope inside it. This method goes the other way: it adds explicit gates and an evidence trail.

That is a deliberate trade for a different context. Shape Up is tuned for product teams shipping features they own end-to-end. This method is tuned for systems where correctness and ownership boundaries matter as much as features, and where an AI agent — which will happily over-reach without guardrails — is part of the loop. The gates and trackers are the price of that control. On lower-risk work, [Right-Sizing the Method](#18-right-sizing-the-method) brings it closer to Shape Up's lightness.

## What is actually new here

The individual pieces are well-established. The contribution is the synthesis and its target:

- framing **phases as concern-isolation for AI agents**, so the agent is not modelling, completing, and hardening in a single uncontrolled diff
- treating **explicit non-goals as drift control** for both agents and junior engineers, where ambiguity reliably causes scope expansion
- making **evidence-based gates the defence against an agent's false "done"**, with "unknown is not pass" as the operating rule

If there is one original idea, it is this: the same discipline that experienced engineers apply by instinct can be written down as guardrails an AI agent has to pass through — and that is what turns fast AI-assisted implementation into controlled delivery.
