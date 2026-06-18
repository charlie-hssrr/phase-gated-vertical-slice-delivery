# Phase B — Tracker

**Branch:** mcp-connectors  
**Spec:** docs/architecture/connectors.md  
**Started:** 2026-04-24  

---

## Status

- 🔴 Not started
- 🟡 In progress
- 🟢 Complete
- ⚠️ Blocked
- ⏸ Deferred

---

## Slices

| # | Slice | Status | PR | Notes |
|---|---|---|---|---|
| B1 | Connector foundation | 🟢 | #123 | Scoped correctly |
| B2 | Folder selection | 🟡 | — | In progress |

---

## Phase Invariants

- [ ] All connectors enforce owner + workspace scope
- [ ] No raw credential storage
- [ ] No cross-tenant access

---

## Decisions Log

| Slice | Decision | Why | Impact |
|---|---|---|---|
| B1 | Use credentialRef instead of raw tokens | Security boundary | Major |

---

## Lessons / Carry-Forward

- Keep connector model strict early
- Avoid mixing sync logic into foundation slices