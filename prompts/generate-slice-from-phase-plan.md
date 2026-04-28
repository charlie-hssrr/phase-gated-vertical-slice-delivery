# Generate Slice Spec From Phase Plan

You are helping create a narrow implementation slice from an existing phase delivery plan.

Do not implement code.

## Input

Use the provided phase plan, tracker, and project context as the source of truth.

## Goal

Create a slice spec that is specific enough for an engineer or coding agent to implement without expanding scope.

## Instructions

- Keep the slice small and reviewable.
- Do not include unrelated cleanup.
- Do not rework earlier completed phases unless explicitly required.
- Include explicit non-goals.
- Include invariants that must remain true.
- Include tests that prove behaviour, not implementation details.
- Include likely files or areas only if supported by the provided context.
- Flag unknowns instead of guessing.

## Required Output Format

Return exactly:

# Slice <ID> — <Title>

## Branch

<branch-name>

## Phase Context

<what phase this belongs to and what has already been completed>

## Goal

## Current Problem

## Scope

### Backend

### Frontend

### Data Model

### Tests

### Documentation

## Invariants

## Out of Scope

## Acceptance Criteria

## Likely Files / Areas

## Validation Plan

## Risks

## Required Implementation Summary Format

The implementer must return exactly:

# <Slice ID> Implementation Summary

# Current Code Paths Inspected

# Changes Applied

# Files Changed

# API Surface Added or Changed

# Security / Scope Enforcement

# Tests Run

# Remaining Risks

# Next Recommended Slice