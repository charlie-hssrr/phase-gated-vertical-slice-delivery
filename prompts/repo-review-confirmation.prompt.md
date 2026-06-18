# Confirm Repo Review Findings Against Current Code

You are confirming a prior repo review against the current state of a POC codebase.

Do not modify code. Do not implement fixes. Do not broaden scope.

## Goal

For each finding in the prior review, determine whether it still holds against the current code, and classify its production relevance.

## Inputs

- Prior repo review: `<path-or-link>`
- Current codebase / branch: `<repo @ commit or branch>`
- Known context: `<incidents, prior decisions, constraints>`

## Instructions

1. Read the prior review findings.
2. For each finding, inspect the current code that it refers to.
3. Determine its current status:
   - **Confirmed** — still true as stated
   - **Partially confirmed** — true in part; describe what holds and what does not
   - **Not confirmed** — cannot reproduce against current code
   - **Outdated** — already fixed or no longer applicable
   - **Not production-blocking** — true, but does not threaten production operation
   - **Requires human decision** — validity or priority depends on a judgement call
4. Cite the file/area you checked for each finding.
5. Do not invent new findings. If you notice something material outside the review, list it separately under "New observations".

## Required Output Format

Return exactly:

# Review Confirmation Summary

## Overview

<One paragraph: how many findings confirmed, dropped, or escalated.>

## Findings

| # | Finding | Status | Evidence (file/area checked) | Notes |
|---|---|---|---|---|

## Requires Human Decision

- <finding and the decision needed>

## New Observations

- <material item found outside the review, or "None">

## Recommended Next Step
