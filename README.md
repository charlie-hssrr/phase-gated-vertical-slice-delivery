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

## Useful for

- platform projects
- AI-enabled systems
- multi-tenant applications
- infrastructure-heavy features
- internal tools
- security-sensitive systems
- projects using AI coding agents

## Start here

- [Full methodology](docs/methodology.md)
- [Project delivery plan template](templates/project-delivery-plan.md)
- [Slice spec template](templates/slice-spec.md)
- [Phase tracker template](templates/phase-tracker.md)
- [Gate tracker template](templates/gate-tracker.md)

## One-line summary

Phase-Gated Vertical Slice Delivery is a phase-gated, slice-driven delivery system for turning ambiguous technical specs into controlled, reviewable, production-grade implementation.

## Examples

- [Example delivery plan](docs/examples/example-delivery-plan.md)
- [Example phase tracker](docs/examples/example-phase-tracker.md)
- [Example gate tracker](docs/examples/example-gate-tracker.md)