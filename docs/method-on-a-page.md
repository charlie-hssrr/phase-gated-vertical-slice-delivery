# The Method on a Page

The whole approach in one screen. For the full version see the [methodology](methodology.md); for the visual version see the [workflow schematic](workflow.html).

---

**The idea.** Build complex systems in ordered phases, deliver them in narrow vertical slices, and let work advance only on evidence — not optimism.

---

## The spine — the smallest honest version

1. State the **one outcome** and what is **out of scope**.
2. Make the change **narrowly**.
3. Show **evidence** it works — a test, a run, a check.
4. **Review the diff** before it lands.

Keep this and you have kept the method. Everything below is added when the risk earns it.

## Build in three phases

| Phase | Make the system… | The question it answers |
|---|---|---|
| **A — Foundation** | structurally correct | Is the model, ownership, and contract right? |
| **B — Completion** | a coherent product | Does the whole flow work end-to-end? |
| **C — Hardening** | safe to operate | Can this run in production? |

Order is the default, not a one-way street: a gap found later becomes a new earlier-phase slice, recorded — never patched in silently.

## Deliver in slices

A slice has **one outcome**, **bounded scope**, **explicit non-goals**, **testable acceptance criteria**, and a **reviewable diff**. It may touch every layer — model, service, UI, tests, telemetry — but solves a single problem. Not horizontal ("all the backend models"), not vague ("improve UX"), not leaky (cleanup because the code was nearby).

## Gate on evidence

Between phases, classify every open item — and **"unknown" is not "pass"**:

- **Hard blocker** — the next phase cannot start.
- **Completion blocker** — the next phase can start, but this one cannot be called done.
- **Deferred / accepted** — tolerated on purpose, and only if named.

## Right-size it

Dial the paperwork to the blast radius. Low-risk slice: scope line, a check, a diff review. High-risk slice (security, multi-tenant, migration, multi-caller API): the full kit. The artefact has to buy down a real risk on *this* slice — if it doesn't, skip it and say so. See [Right-Sizing the Method](methodology.md#18-right-sizing-the-method).

## The ten rules

1. Build foundations before product completion.
2. Build product completion before hardening.
3. Slice vertically around outcomes.
4. Keep every slice narrow and reviewable.
5. State non-goals aggressively.
6. Enforce invariants in the correct layer.
7. Track decisions as they happen.
8. Use gates to separate optimism from evidence.
9. The human reviewer owns completion, not the AI.
10. Never move forward with unknowns disguised as passes.

---

> A phase-gated, slice-driven delivery system for turning ambiguous specs into controlled, reviewable, production-grade implementation.
