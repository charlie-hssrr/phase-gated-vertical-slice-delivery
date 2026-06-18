# POC-to-Production Hardening Mode

This is an operating mode for the Phase-Gated Vertical Slice Delivery methodology.

It applies when a project **already exists as a working proof of concept** and now needs to become production-ready without a rewrite.

It extends the methodology. It does not replace the Phase A / Phase B / Phase C model. Use the standard model for new or ambiguous builds. Use this mode when there is working code and a set of review findings to act on.

---

## When to use this mode

Use POC-to-production mode when:

- a working POC, prototype, or internal build already exists
- the core product loop already runs end-to-end
- a repo review, audit, or assessment has produced a list of findings
- the goal is controlled hardening, not new product scope
- you need to convert findings into reviewable slices with clear gates

## When not to use this mode

Do not use this mode when:

- there is no working system yet — use Phase A — Foundation instead
- the core model is still unstable or contested — finish foundation first
- the work is primarily new product completion — use Phase B — Completion
- the request is an open-ended rewrite — that is a new build, not hardening

If you are unsure whether the foundation is stable, treat it as a new build and run the standard phases.

---

## Delivery shape

The shape is the same as the core methodology — phases, vertical slices, invariants, gates, trackers — applied to an existing codebase.

The difference is the entry point and the phase set:

- The entry point is a **repo review**, not a raw spec.
- Findings are **classified** before any become work.
- Phases are tuned for hardening, not greenfield construction.
- Slices are **narrow remediation units**, scoped to address specific findings.

The control surface remains the tracker. The handoff object after every slice is an [implementation report](../templates/implementation-report.md).

---

## Recommended phases

These replace the A/B/C labels for hardening work. They map onto the same gate discipline.

### REVIEW

Establish ground truth.

- Confirm the prior review findings against the current code.
- Classify every finding (see below).
- Define the core invariants the production system must hold.
- Produce a delivery plan: phases, slices, first slice.

Output: a classified finding list and a [repo-review-to-phase-plan](../templates/repo-review-to-phase-plan.md).

### HARDEN

Remediate the findings that matter.

- Implement production blockers first, then hardening requirements.
- One narrow slice per finding or tightly related finding cluster.
- Each slice returns an implementation report.
- Maintainability improvements only where they reduce production risk now.

Output: merged remediation slices with evidence.

### OPERATE

Make the system safe to run.

- Confirm observability: logs, metrics, error surfaces.
- Confirm failure handling and recovery paths.
- Confirm configuration, secrets, and environment handling.
- Document operational dependencies and runbook steps.

Output: a system that can be operated and debugged in production.

### CLOSEOUT

Decide explicitly whether it is production-ready.

- Confirm all production blockers are resolved.
- Confirm accepted risks are named and signed off.
- Confirm deferred work is recorded, not lost.
- Make a clear go / no-go decision with evidence.

Output: a production-readiness decision backed by tracker state.

---

## Classifying repo review findings

A review produces findings. Findings are not tickets. Classify each one before deciding what becomes work.

| Classification | Meaning | Default action |
|---|---|---|
| Production blocker | Will cause incidents, data loss, or security exposure in production | Must fix before CLOSEOUT |
| Hardening requirement | Real risk that should be addressed before broad use | Schedule into HARDEN |
| Maintainability improvement | Improves the code but does not reduce production risk now | Defer unless cheap and adjacent |
| Accepted risk | Real, understood, and deliberately tolerated | Record and sign off, do not fix |
| Deferred | Valid but out of scope for this hardening pass | Record in carry-forward |
| Rejected / not valid | Incorrect, outdated, or based on a misread of the code | Record why, take no action |

### Rule: not every review comment becomes a ticket

A review comment is an observation, not a mandate. Many findings are correct as observations but wrong as priorities. The classification step exists to filter, not to convert one-to-one.

Record the disposition of every finding so the decision is auditable — including the ones you reject or accept.

---

## Emphasis: production risk over architectural purity

Hardening is about reducing the risk of operating the system, not about making the architecture ideal.

- Prefer fixing a real failure mode over refactoring a working component.
- Prefer a narrow, safe change over a broad "while we're here" cleanup.
- Treat "this isn't how I'd design it" as a maintainability improvement, not a blocker, unless it causes a concrete production risk.

Architectural purity is a deferred concern unless it is also a production risk.

## Emphasis: narrow remediation slices

Each slice should address one finding or one tightly related cluster.

- Keep the blast radius small enough to review and roll back.
- Do not bundle unrelated findings because they touch nearby code.
- Split a slice when one part changes behavior and another only observes.
- A finding that needs a large change is a signal to split it, not to widen the slice.

Narrow slices are what keep hardening controlled rather than turning into an unbounded rewrite.

---

## Recommended sequence

Run the mode as a short, named sequence from review to gate:

```text
REVIEW1   — run the repo review
PLAN1     — convert findings into a hardening plan
HARDEN1+  — execute bounded remediation slices
CLOSEOUT  — confirm the production-readiness gate
```

- **REVIEW1** — run the [POC-to-production repo review prompt](../prompts/poc-to-production-repo-review.prompt.md) against the existing POC. See the [worked review example](../examples/poc-to-production-review-example.md).
- **PLAN1** — convert the classified findings into a plan using the [repo review to phase plan template](../templates/repo-review-to-phase-plan.md). See the [worked hardening plan example](../examples/poc-to-production-hardening-plan-example.md).
- **HARDEN1+** — implement one bounded slice at a time with the [slice implementation prompt](../prompts/slice-implementation.prompt.md), returning an [implementation report](../templates/implementation-report.md) each time.
- **CLOSEOUT** — make the go / no-go decision with the [phase closeout prompt](../prompts/phase-closeout.prompt.md).

---

## Related material

- [POC-to-production repo review prompt](../prompts/poc-to-production-repo-review.prompt.md) — REVIEW1 entry point
- [Repo review example](../examples/poc-to-production-review-example.md) — worked review output
- [Hardening plan example](../examples/poc-to-production-hardening-plan-example.md) — worked plan output
- [Lightweight engineer guide](lightweight-engineer-guide.md) — the per-slice loop to follow
- [Implementation report template](../templates/implementation-report.md) — the handoff object
- [Repo review to phase plan template](../templates/repo-review-to-phase-plan.md) — convert findings to a plan
- [Methodology](methodology.md) — the full phase/slice/gate model
