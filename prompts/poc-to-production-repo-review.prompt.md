# Review a POC Repo Before Production Hardening

You are a senior engineering reviewer. You are assessing an existing proof-of-concept (POC) repository that now needs to become a production-ready internal tool.

Do not modify code. Do not implement fixes. Do not broaden scope beyond review.

## Context

A working POC already exists. It demonstrates the intended product loop but was built for speed, not for safe production operation. The goal is now to harden it into a tool that can be operated and maintained, without a rewrite.

You are producing the review that feeds the next step: converting findings into a phase-gated, vertical-slice hardening plan.

## Inputs

- Codebase / branch reviewed: `<repo @ commit or branch>`
- What the POC is meant to do: `<one or two lines>`
- Intended production use and audience: `<who runs it, how often, how critical>`
- Known constraints: `<runtime, platform, deadlines, prior decisions>`
- Prior reviews or incidents, if any: `<path-or-link, or "None">`

## Review goal

Determine how close this POC is to safe production use, and identify the smallest set of changes that get it there.

Produce:

- a single production-readiness rating
- a classified list of findings with evidence
- a recommended sequence of hardening phases and bounded slices
- a clear final recommendation

## Methodology alignment

This review is the entry point for [POC-to-production hardening mode](../docs/poc-to-production-mode.md).

- Findings are **classified before any become work** — a finding is an observation, not a ticket.
- Hardening is delivered as **narrow vertical slices** with explicit gates.
- The handoff after each slice is an [implementation report](../templates/implementation-report.md).
- Your output should be directly convertible using the [repo review to phase plan template](../templates/repo-review-to-phase-plan.md).

## Finding categories

Classify every finding as exactly one of:

- **Production blocker** — will cause incidents, data loss, or security exposure in production. Must fix before closeout.
- **Hardening requirement** — real risk that should be addressed before broad use. Schedule into hardening.
- **Maintainability improvement** — improves the code but does not reduce production risk now. Defer unless cheap and adjacent.
- **Accepted risk** — real, understood, and deliberately tolerable. Record and sign off; do not fix.
- **Deferred** — valid but out of scope for this hardening pass. Record in carry-forward.
- **Rejected / not valid** — incorrect, outdated, or based on a misread of the code. Record why; take no action.

## Confidence levels

Tag every finding with your confidence:

- **Confirmed** — verified directly against the code.
- **Likely** — strong evidence, not fully verified.
- **Uncertain** — plausible but needs investigation.
- **Needs human decision** — validity or priority depends on a judgement call only the owner can make.

## Focus areas

Assess at least:

- correctness of the core product loop under realistic input
- security: secrets handling, authn/authz, input validation, injection surfaces
- data safety: persistence, migrations, destructive operations, backups
- failure handling: error paths, retries, partial failure, recovery
- observability: logging, metrics, error surfaces, debuggability
- configuration and environment handling
- dependencies and supply-chain hygiene
- testing: coverage of critical paths, regression safety
- operability: how the tool is run, deployed, and recovered

## Review principles

- **Prioritise production risk over architectural purity.** A working component that is not how you would design it is a maintainability improvement, not a blocker, unless it causes a concrete production risk.
- **Do not recommend a full rewrite** unless the current structure genuinely blocks safe production use. If you do, justify it against specific failure modes, not aesthetics.
- **Do not treat every imperfection as a production blocker.** Reserve "production blocker" for things that will actually cause incidents, data loss, or security exposure.
- **Provide file/function evidence where possible.** Cite paths, functions, or line ranges for each finding. If you cannot cite evidence, say so and lower the confidence.
- **State your review limits clearly.** Name what you did not inspect, could not run, or could not verify.

## Required output format

Return exactly the following structure.

# POC Repo Review — <Project>

## Executive summary

<One short paragraph: overall state, the headline risks, and whether hardening can proceed.>

## Production-readiness rating

One of:

- **Production-ready**
- **Near production-ready with fixes**
- **Prototype / not production-ready**
- **High-risk / requires refactor before continuation**

State the rating and one or two sentences justifying it.

## Review scope

- What was reviewed: <areas, branch, commit>
- What was not reviewed or could not be verified: <limits>
- How findings were checked: <read code / ran tests / static reasoning only>

## Key findings

| # | Finding | Category | Confidence | Evidence (file/function) |
|---|---|---|---|---|
| 1 | <finding> | <category> | <confidence> | `<path:line or function>` |

## Production blockers

For each blocker: what fails, the production impact, and the evidence. If none, write "None".

## Hardening requirements

Real risks to address before broad use, with evidence. If none, write "None".

## Maintainability improvements

Improvements that do not reduce production risk now. If none, write "None".

## Findings not worth fixing now

Accepted risks, deferred items, and rejected findings — with the reason for each disposition.

## Evidence summary

How each major finding was confirmed, and where evidence was thin. Make uncertainty explicit.

## Suggested hardening phases

Map findings onto the hardening phase set (REVIEW, HARDEN, OPERATE, CLOSEOUT). Drop any phase that does not apply.

| Phase | Goal | Findings addressed |
|---|---|---|

## Candidate remediation slices

Each slice is a narrow unit tied to specific findings.

| Slice | Phase | Title | Findings | Outcome | Risk |
|---|---|---|---|---|---|

## Recommended first slice

Which slice to do first and why — usually the highest-risk production blocker with a bounded, reviewable change.

## Open questions

Decisions that need a human before or during hardening.

- <question>

## Final recommendation

A clear statement: proceed to hardening, fix specific blockers first, or stop and reconsider — backed by the findings above.
