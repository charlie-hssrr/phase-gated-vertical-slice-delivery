# Phase-Gated Vertical Slice Delivery

## Introduction

Phase-Gated Vertical Slice Delivery is a structured engineering methodology for building complex systems through controlled, incremental progress.

It was developed from practical experience delivering systems where:

- architecture is evolving while implementation is underway
- correctness (data model, security, ownership) matters as much as feature delivery
- AI coding agents are used to accelerate implementation but require strict guardrails
- traditional Agile approaches proved too loose for managing architectural risk and scope drift

The methodology combines several established principles:

- Vertical Slice Architecture (build outcomes, not layers)
- Domain-Driven Design (focus on entities, boundaries, and invariants)
- Stage-Gated delivery (explicit phase transitions with evidence-based gates)
- Small-batch engineering (frequent, controlled increments)
- Secure systems thinking (no implicit trust, no silent defaults)

However, it intentionally removes ceremony and focuses on execution discipline.

The motivation behind the approach is simple:

> Turn ambiguous specs into controlled, reviewable, production-grade systems without relying on large upfront design or uncontrolled iteration.

Key ideas:

- Separate *structure*, *product completeness*, and *hardening* into distinct phases
- Deliver work in small, vertically complete slices
- Define and enforce system invariants early
- Use gates to prevent premature progression
- Maintain a living record of decisions and lessons

This methodology is especially effective for:

- platform and infrastructure projects
- AI-enabled systems
- multi-tenant or security-sensitive applications
- projects with evolving architecture

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

A slice is a small, reviewable unit of delivery that produces a coherent outcome.

A good slice may touch multiple layers:

- data model
- backend service
- frontend state
- UI behavior
- tests
- telemetry
- documentation

But it should only solve one defined problem.

A bad slice is either:

- too horizontal: “build all backend models” with no usable outcome
- too broad: “implement the whole connector platform”
- too vague: “improve workspace UX”
- too leaky: starts pulling in unrelated cleanup because the code is nearby

## 1.3 Invariants before features

Every project has rules that must never be violated.

Examples:

- every record must have a tenant boundary
- every workspace must have a default assistant
- every query must enforce ownership scope
- every exported artefact must include provenance
- every background job must be traceable

These are not preferences. They are system invariants.

The methodology requires invariants to be identified early, reviewed at every slice, and enforced in the correct layer. If an invariant defines correctness, it belongs in backend or shared domain logic, not only in frontend behavior.

## 1.4 Explicit non-goals are a delivery tool

Every phase and slice must say what it is not doing.

This is especially important when working with AI coding agents or junior engineers. Ambiguity creates scope expansion.

A clear out-of-scope section protects the project from:

- speculative abstractions
- premature polish
- unplanned migrations
- cleanup rabbit holes
- broad rewrites disguised as small fixes

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

# 18. Final Rule Set

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

# 19. One-Line Summary

This methodology is a phase-gated, slice-driven delivery system for turning ambiguous technical specs into controlled, reviewable, production-grade implementation.
