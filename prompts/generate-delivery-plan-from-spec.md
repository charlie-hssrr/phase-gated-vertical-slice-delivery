# Generate Phase-Gated Delivery Plan From Spec

You are helping convert a raw technical spec into a phase-gated vertical slice delivery plan.

Do not implement code.

## Input

Use the provided spec as the source of truth.

## Goal

Create a delivery plan that breaks the work into:

- Phase A — Foundation
- Phase B — Completion
- Phase C — Hardening

Each phase must contain narrow vertical slices with clear outcomes.

## Instructions

1. Extract the core model:
   - entities
   - relationships
   - ownership boundaries
   - lifecycle states
   - external systems

2. Extract invariants:
   - security rules
   - ownership rules
   - data correctness rules
   - compatibility requirements

3. Separate the work into phases:
   - Phase A: structure, model, contracts, core invariants
   - Phase B: usable end-to-end product flow
   - Phase C: resilience, observability, performance, security hardening

4. Define slices:
   - each slice must have one clear outcome
   - avoid broad horizontal slices
   - avoid speculative future work
   - split frontend/backend only when risk or review scope justifies it

5. Add gates:
   - start blockers
   - completion blockers
   - deferred risks

## Required Output Format

Return exactly:

# Delivery Plan — <Project Name>

## Objective

## Current State

## Target State

## Core Invariants

## Phase A — Foundation

### Goal

### Slices

| Slice | Title | Outcome | Risk |
|---|---|---|---|

### Phase A Gate

## Phase B — Completion

### Goal

### Slices

| Slice | Title | Outcome | Risk |
|---|---|---|---|

### Phase B Gate

## Phase C — Hardening

### Goal

### Slices

| Slice | Title | Outcome | Risk |
|---|---|---|---|

### Production Gate

## Key Risks

## Recommended First Slice