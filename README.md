# Phase-Gated Vertical Slice Delivery

A practical delivery methodology for turning ambiguous technical specs into controlled, reviewable, production-grade implementation.

It combines:

- phase-gated delivery
- vertical slice architecture
- invariant-driven design
- small-batch engineering
- AI-agent-assisted implementation discipline

The method is designed for complex software projects where architecture, implementation, and validation evolve together.

## Why this exists

Modern software delivery often fails in the gap between planning and implementation.

Specs are ambiguous. Architecture changes mid-flight. AI coding agents can move fast but drift without guardrails. Teams often mix foundational modelling, product completion, hardening, and cleanup in the same pass.

Phase-Gated Vertical Slice Delivery solves that by separating work into clear phases and narrow, evidence-backed slices.

## Core idea

Build in phases:

| Phase | Purpose |
|---|---|
| Phase A — Foundation | Make the system structurally correct |
| Phase B — Completion | Make the product coherent and usable end-to-end |
| Phase C — Hardening | Make the system reliable, secure, observable, and production-ready |

Build each phase through vertical slices:

> A slice is a small, reviewable unit of delivery that produces one coherent system outcome.

## Choose your workflow

| Situation | Use |
|---|---|
| New or ambiguous build | [Phase A / B / C methodology](docs/methodology.md) |
| Existing POC needing production hardening | [POC-to-production mode](docs/poc-to-production-mode.md) |
| AI-assisted implementation | [Slice prompt](prompts/slice-implementation.prompt.md) + [implementation report](templates/implementation-report.md) |
| Reviewing a repo before hardening | [POC-to-production repo review prompt](prompts/poc-to-production-repo-review.prompt.md) |
| Closing a phase | [Phase closeout prompt](prompts/phase-closeout.prompt.md) |

## Useful for

- platform projects
- AI-enabled systems
- multi-tenant applications
- infrastructure-heavy features
- internal tools
- security-sensitive systems
- projects using AI coding agents

## Start here

- [The method on a page](docs/method-on-a-page.md) — the whole approach in one screen
- [Documentation index](docs/index.md) — full navigation
- [Visual workflow schematic](docs/workflow.html) — animated, single-file overview ([live](https://charlie-hssrr.github.io/phase-gated-vertical-slice-delivery/) once Pages is enabled)
- [Full methodology](docs/methodology.md)
- [Project delivery plan template](templates/project-delivery-plan.md)
- [Slice spec template](templates/slice-spec.md)
- [Phase tracker template](templates/phase-tracker.md)
- [Gate tracker template](templates/gate-tracker.md)
- [Methodology closeout note](docs/methodology-closeout.md)

## POC-to-production hardening

Already have a working POC and a repo review to act on? Use the hardening mode — same phase/slice/gate discipline, applied to existing code, with findings classified before any become work.

- [POC-to-production mode](docs/poc-to-production-mode.md)
- [Lightweight engineer guide](docs/lightweight-engineer-guide.md)
- [Implementation report template](templates/implementation-report.md)
- [Repo review to phase plan template](templates/repo-review-to-phase-plan.md)

Prompts:

- [POC-to-production repo review](prompts/poc-to-production-repo-review.prompt.md)
- [Confirm repo review findings](prompts/repo-review-confirmation.prompt.md)
- [Review to delivery plan](prompts/review-to-delivery-plan.prompt.md)
- [Slice implementation](prompts/slice-implementation.prompt.md)
- [Implementation review](prompts/implementation-review.prompt.md)
- [Phase closeout](prompts/phase-closeout.prompt.md)

Worked examples:

- [Spec-to-closeout walkthrough](examples/spec-to-closeout-walkthrough.md) — the full loop, end to end
- [Repo review example](examples/poc-to-production-review-example.md)
- [Hardening plan example](examples/poc-to-production-hardening-plan-example.md)

## One-line summary

Phase-Gated Vertical Slice Delivery is a phase-gated, slice-driven delivery system for turning ambiguous technical specs into controlled, reviewable, production-grade implementation.

## Examples

- [Example delivery plan](docs/examples/example-delivery-plan.md)
- [Example phase tracker](docs/examples/example-phase-tracker.md)
- [Example gate tracker](docs/examples/example-gate-tracker.md)
- [Example case study](docs/examples/case-study-connectors.md)