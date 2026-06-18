# Repo Review → Phase Plan — <Project Name>

Converts repo review findings into a phase-gated, vertical-slice hardening plan for an existing POC.

Use this with [POC-to-production hardening mode](../docs/poc-to-production-mode.md).

---

## Source material

- Review document(s): <link or path>
- Reviewers / date: <who, when>
- Codebase / branch reviewed: <repo @ commit or branch>
- Other inputs: <issues, incidents, prior decisions>

## Current project state

<What exists today. What the POC does. What already works end-to-end. Known weak spots.>

## Target production state

<What must be true for this to run safely in production. The bar this plan is working toward.>

## Finding classification

Classify every finding. Not every finding becomes a slice. See [classification guidance](../docs/poc-to-production-mode.md#classifying-repo-review-findings).

| # | Finding | Classification | Disposition |
|---|---|---|---|
| 1 | <finding> | Production blocker / Hardening requirement / Maintainability improvement / Accepted risk / Deferred / Rejected | <slice ID, deferred, accepted, or reason rejected> |

Classifications:

- **Production blocker** — must fix before closeout
- **Hardening requirement** — schedule into HARDEN
- **Maintainability improvement** — defer unless cheap and adjacent
- **Accepted risk** — record and sign off, do not fix
- **Deferred** — valid but out of scope this pass
- **Rejected / not valid** — record why, no action

## Core invariants

The rules the production system must hold throughout hardening:

- <invariant>
- <invariant>

## Proposed phases

Use the hardening phase set. Drop any phase that does not apply.

| Phase | Goal | Findings addressed |
|---|---|---|
| REVIEW | Confirm findings, classify, plan | — |
| HARDEN | Remediate blockers and hardening requirements | <#s> |
| OPERATE | Observability, failure handling, config, runbook | <#s> |
| CLOSEOUT | Production go / no-go decision | — |

## Proposed slices

Each slice is a narrow remediation unit tied to specific findings.

| Slice | Phase | Title | Findings | Outcome | Risk |
|---|---|---|---|---|---|
| H1 | HARDEN | <title> | #<n> | <outcome> | Low / Med / High |

## Recommended first slice

<Which slice to do first and why — usually the highest-risk production blocker with a bounded change.>

## Explicit non-goals

What this hardening pass will not do:

- <non-goal>
- <non-goal>

## Open questions

Decisions that need a human before or during delivery:

- <question>
- <question>
