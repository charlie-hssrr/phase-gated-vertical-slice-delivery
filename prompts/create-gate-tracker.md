# Create Phase Gate Tracker

You are creating a gate tracker between two delivery phases.

Do not implement code.

## Goal

Define the evidence required to decide whether the next phase can start.

## Instructions

- Separate start blockers from completion blockers.
- Unknown is not pass.
- Deferred work must be named.
- Do not hide technical blockers as process issues.
- Do not treat process visibility as a technical blocker.
- Every gate item needs evidence.

## Required Output Format

Return exactly:

# Phase <X> → Phase <Y> Gate Tracker

## Gate Policy

## Start Blockers

## Completion Blockers

## Deferred From Previous Phase

## Gate Status Table

| Gate Item | Status | Evidence | Notes / Action |
|---|---|---|---|

## Required Checks

## Manual Validation Log

| Check | Status | Evidence | Date | Notes |
|---|---|---|---|---|

## Final Readiness Decision

Proceed / Do not proceed / Proceed with accepted risks

Reason:
<short rationale>