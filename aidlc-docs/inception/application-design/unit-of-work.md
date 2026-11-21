# Units of Work: Course AIdvisor

## Purpose and Constraints

The units below are development groupings for one local Python application. They are organized by technical capability, use stable typed contracts, and progress in dependency order. Each capability must be completed across the stories that depend on it before moving to the next capability. The team moves as one person; this plan assigns no owners or parallel work.

All runtime modules remain inside one Streamlit application process, with local Ollama as a local runtime dependency. Public Cornell and CU Reviews data is accessed only through fixed-host read-only adapters. Student profile, conversation, and plan data remain in active session memory and are never written to persistent stores or logs.

## Unit 1: Shared Contracts and Public Data Foundations

**Capability:** Typed domain models, provenance, source adapters, and curated program catalog data.

**Responsibilities:**

- Define shared Pydantic records and request/result contracts for courses, programs, reviews, citations, source issues, student session snapshots, and plans.
- Implement fixed-host Cornell roster and CU Reviews adapters that validate inputs and responses and preserve provenance.
- Implement Cornell catalog ingestion, structured requirement records, coverage validation against the undergraduate program table, and the curated read-only repository.
- Support a lightweight human review for every candidate catalog re-scrape before replacement of the active curated snapshot.
- Keep student data out of adapters and persistent catalog records.

**Story coverage:** Provides grounded facts and types needed by every story; directly supports course and requirement facts in J1-S3, J1-S4, J2-S1 through J2-S6, and J3-S1 through J3-S3.

**Boundary:** Public source access and public curated program data only. No mapping decisions, plan validation, UI, agent orchestration, or student persistence.

## Unit 2: Deterministic Planning and Review Analysis

**Capability:** Requirement mapping, plan validation, plan assembly/refinement, and local review sentiment scoring.

**Responsibilities:**

- Map completed and proposed courses to curated requirements, returning apparent satisfied/remaining status, citations, and uncertainty.
- Validate represented requirement coverage, known prerequisite ordering, forbidden overlaps, and supported constraints; unresolved violations prevent a validated status.
- Assemble and refine plans within the approved four-semester MVP horizon and return typed findings to callers.
- Compute a locally derived CU Reviews sentiment score with review count, citations, and an insufficient-data indicator; keep it distinct from source ratings.
- Use source and catalog records through the shared typed interfaces from Unit 1.

**Story coverage:** Implements core outcomes for J1-S2 through J1-S5, J2-S1 through J2-S6, and course review comparison in J1-S3 and J3-S1.

**Boundary:** Deterministic domain results and derived review sentiment. No conversational policy selection, UI rendering, arbitrary network access, or official academic decisions.

## Unit 3: Agent Orchestration and Fixed Tools

**Capability:** Local conversation service and validated capability selection.

**Responsibilities:**

- Manage multi-turn requests through local Ollama and expose a fixed registry of narrow tools.
- Validate tool names and arguments with typed contracts, dispatch to the source/planning capabilities, and return cited structured evidence.
- Preserve deterministic findings, source issues, and uncertainty when explaining results; do not invent facts or claim official advising authority.
- Treat user messages and retrieved review text as untrusted input and keep endpoints outside model control.

**Story coverage:** Supports conversational intake, follow-up, explanation, refinement, and advising handoff across all three journeys, especially J1-S1, J1-S5, J2-S4, J2-S5, J2-S6, J3-S2, and J3-S3.

**Boundary:** Calls local Ollama and in-process application interfaces only. Does not own session persistence, source adapters, mapping algorithms, or UI rendering.

## Unit 4: Streamlit Experience and Session Integration

**Capability:** User-facing chat, active session state, and presentation of grounded results.

**Responsibilities:**

- Collect messages and supported student context, hold profile/conversation/plan only in local active session memory, and clear it at session end/reset.
- Connect UI to the agent through fixed typed interfaces.
- Render course and requirement citations, sentiment labels, plan findings, uncertainty, missing data, and advising handoffs clearly.
- Avoid exposing internal module boundaries as separate product features and avoid logging or persisting student content.

**Story coverage:** Delivers the student outcomes and privacy behavior in every story, including explicit session privacy in J1-S1 and J1-S6 and user-facing handoffs in J2-S5, J2-S6, and J3-S3.

**Boundary:** UI and active session state only. No external source access, direct model tool selection, or authoritative planning logic.

## Sequencing and Completion Rule

Complete Unit 1 contracts and source data capabilities across all dependent stories first. Then complete Unit 2 deterministic planning and review analysis across every dependent story. Next complete Unit 3 orchestration across those same supported capabilities. Complete Unit 4 UI integration so the full story outcomes are presented end-to-end. At each step, integrate via the fixed typed Python interfaces and preserve complete story coverage; the sequence is not a team ownership or parallelization plan.

## Greenfield Code Organization

Use one Python package rooted at `src/aidvisor/`, grouped by capability, with a mirrored `tests/` tree. A starter layout is:

```text
src/aidvisor/
  models/
  sources/
    roster.py
    cu_reviews.py
    catalog.py
  catalog/
  planning/
    mapping.py
    validation.py
    orchestrator.py
    sentiment.py
  agent/
  ui/
tests/
  models/
  sources/
  catalog/
  planning/
  agent/
  ui/
```

This layout describes logical modules in the single local application. It introduces no separately deployed services or network APIs. Exact files may follow implementation needs while retaining the stable capability boundaries and shared contracts.

## Extension Compliance

- **Security Baseline (limited prototype scope):** Compliant. The units preserve fixed source adapters, typed validation, local-only student session data, untrusted-input handling, and no student-content logging/persistence.
- **Resiliency Baseline:** N/A; disabled by team decision.
- **Property-Based Testing (partial):** N/A for unit decomposition. Applicable test design and configured PBT requirements will be addressed in later construction stages.
