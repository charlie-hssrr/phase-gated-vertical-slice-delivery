# Case Study — Building a Workspace Connector System (Google Drive)

## Context

A multi-tenant platform needed to support external data sources, starting with Google Drive.

The goal was not just to “connect to Drive”, but to introduce a structured connector system that:

- respects workspace and tenant boundaries
- supports controlled data ingestion
- integrates with an existing indexing and retrieval pipeline
- avoids leaking external system complexity into the product layer

The system already had:

- workspace + ownership model (`ownerPartitionKey`, `workspaceId`)
- ingestion pipeline (files → indexing → retrieval)
- Azure-based storage and search infrastructure

What was missing was a safe, extensible connector layer.

---

## Problem

Initial state had several gaps:

- No formal connector abstraction
- No consistent way to represent external data sources
- No separation between:
  - source access (Drive)
  - ingestion
  - indexing
- Risk of:
  - cross-tenant data access
  - raw credential leakage
  - tight coupling between external APIs and retrieval logic

There was also a constraint:

> The architecture was still evolving, and implementation was being accelerated using AI-assisted coding.

A loose approach would have resulted in scope creep and inconsistent patterns.

---

## Approach

Used a phase-gated, vertical slice approach.

### Phase A — Foundation (already completed)

Established:

- ownership model (`ownerPartitionKey`, `workspaceId`)
- ingestion pipeline contracts
- search/indexing structure
- backend service patterns

This allowed connector work to plug into a stable base.

---

## Phase B — Connector Delivery

### Strategy

Treat connectors as:

> configuration + controlled ingestion pipelines  
not live query execution systems

MCP (Model Context Protocol) was positioned as:

> a source-access layer only

All system responsibility remained inside the platform:

- connector configuration
- credential references
- sync orchestration
- artefact creation
- indexing
- retrieval
- provenance

---

## Slice B1 — Connector Foundation

### Goal

Introduce a safe, workspace-scoped connector model and API without implementing sync or external calls.

### Scope

Implemented:

- Connector data model
- Workspace-scoped connector service
- Connector API routes
- Credential reference abstraction (`credentialRef`)
- MCP client interface (stub only)
- Validation and ownership enforcement
- Unit and route tests

Explicitly excluded:

- OAuth flow
- token storage
- Drive API calls
- folder listing
- sync jobs
- ingestion changes
- frontend UI

---

## Key Decisions

### 1. Connector scope is strict

Every connector requires:

- `ownerPartitionKey`
- `workspaceId`

Access is always enforced on both.

Connector ID alone is never sufficient.

---

### 2. No credential storage in core database

- No OAuth tokens stored in Cosmos
- Only `credentialRef` is persisted
- Credential resolution is abstracted behind a resolver interface

This isolates security-sensitive logic from the data model.

---

### 3. MCP is not part of the product contract

- MCP client introduced as an interface only
- No direct MCP calls wired into business logic
- No MCP payloads allowed into prompt construction

Prevents external system shape from leaking into the platform.

---

### 4. Canonical naming enforced early

- `connectorType = "google_drive"`
- `sourceSystem = "google_drive"`

Avoided introducing alternate names like `gdrive`.

This reduces future migration and mapping complexity.

---

### 5. Sync is explicitly deferred

Connector foundation does not include:

- folder selection
- sync jobs
- ingestion

This kept the slice small and prevented mixing concerns.

---

## Validation

Tests focused on behaviour, not implementation:

- connector creation requires ownership fields
- invalid connector types rejected
- service layer enforces:
  - owner boundary
  - workspace boundary
- routes cannot access connectors outside scope
- credential fields cannot store raw tokens
- `credentialRef` persists as reference only

Manual checks confirmed:

- no cross-tenant access possible through API
- no unsafe defaults introduced

---

## Outcome

After B1:

- connectors can be created, listed, updated, and deleted safely
- system enforces strict ownership boundaries
- credential handling is abstracted correctly
- foundation is ready for:
  - folder selection (B2)
  - sync pipeline (B3)

Importantly:

> No external API complexity has leaked into the core system yet.

---

## What Went Well

- Narrow slice prevented scope creep
- Explicit out-of-scope avoided accidental sync implementation
- Security rules enforced at the model + service layer (not UI)
- Tests validated real failure modes (wrong owner, wrong workspace)
- MCP integration contained behind interface

---

## What Was Avoided

- Storing OAuth tokens in application database
- Mixing ingestion logic into connector setup
- Building UI before backend contracts were stable
- Allowing connector ID to act as an access control mechanism
- Treating Drive as a live query system

---

## Remaining Work (Next Slices)

### B2 — Source Selection

- folder selection
- shared drive selection
- validation of selected sources

### B3 — Sync Pipeline

- scheduled sync jobs
- ingestion integration
- artefact creation
- indexing into search

### Phase C

- retry logic
- failure handling
- observability
- performance tuning
- security audit

---

## Key Takeaway

The main gain was not speed.

It was control.

By isolating:

- model
- ownership
- credentials
- external access

the system avoided early coupling to Google Drive and kept the architecture clean.

Each slice introduced capability without increasing ambiguity.