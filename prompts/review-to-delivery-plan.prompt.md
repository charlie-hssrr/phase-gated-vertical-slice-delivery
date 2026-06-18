# Convert Repo Review Into a Hardening Delivery Plan

You are converting confirmed repo review findings into a phase-gated, vertical-slice hardening plan for an existing POC.

Do not implement code. Do not start fixing findings.

## Inputs

- Confirmed review findings: `<path-or-link>`
- Current project state: `<short description>`
- Target production state: `<short description>`

## Goal

Produce a hardening plan that:

- classifies every finding
- defines core invariants
- groups remediation into the hardening phases (REVIEW, HARDEN, OPERATE, CLOSEOUT)
- breaks work into narrow vertical slices tied to specific findings
- names a recommended first slice

## Instructions

1. Classify every finding. Not every finding becomes a slice. Use:
   - Production blocker
   - Hardening requirement
   - Maintainability improvement
   - Accepted risk
   - Deferred
   - Rejected / not valid
2. Extract the core invariants the production system must hold.
3. Assign findings to phases:
   - HARDEN: production blockers and hardening requirements
   - OPERATE: observability, failure handling, config, runbook
   - Defer maintainability improvements unless cheap and adjacent
4. Define slices:
   - one slice per finding or tightly related cluster
   - keep blast radius small and reviewable
   - split a slice when one part changes behavior and another only observes
5. Prioritise production risk over architectural purity.
6. State explicit non-goals and open questions.

## Required Output Format

Return exactly:

# Hardening Delivery Plan — <Project Name>

## Current State

## Target Production State

## Finding Classification

| # | Finding | Classification | Disposition |
|---|---|---|---|

## Core Invariants

## Phases

| Phase | Goal | Findings addressed |
|---|---|---|

## Slices

| Slice | Phase | Title | Findings | Outcome | Risk |
|---|---|---|---|---|---|

## Recommended First Slice

## Explicit Non-Goals

## Open Questions
