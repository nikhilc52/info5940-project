# Application Design Plan: Course AIdvisor

## Context Loaded

- Approved requirements, vision, technical environment, and execution plan.
- Approved personas and user stories, including the Economics major-change and ORIE graduation-preparation outcomes.
- Existing project constraints: Python 3.12+, Streamlit, local Ollama with `qwen3:8b`, Pydantic, `httpx`, and read-only HTTPS calls to Cornell Roster, CU Reviews, and Cornell Programs Catalog sources.
- Program requirement coverage: curated requirement data is expected for every Cornell undergraduate program listed in the Cornell Programs Catalog. The input documents identify catalog web scraping as the acquisition approach; design will specify a validated, source-cited, read-only repository and surface missing or uncertain coverage.
- Privacy and trust constraints: student information remains only in the active chat/application session and is not persisted or sent to external services. Results must be validated and source-grounded to support user trust.
- Security Baseline is enabled at limited prototype-standard scope. Resiliency Baseline is disabled. Property-Based Testing is partially enabled and does not determine application component boundaries.

## Design Scope

Define high-level component responsibilities and interfaces, method signatures, orchestration services, dependencies, and data flow for a local conversational course planning assistant. Detailed planning rules belong in Functional Design. This stage does not authorize new external services, hosted models, user accounts, persistence, or product capabilities outside the approved requirements.

## Application Design Plan

- [x] Analyze approved requirements, stories, technical constraints, and extension configuration.
- [x] Record and analyze team answers to the design questions; capture the catalog coverage and scraping constraint.
- [x] Confirm catalog coverage and refresh handling and explicitly approve this plan before generating design artifacts.
- [x] Generate `application-design/components.md` with component purposes, responsibilities, and high-level interfaces.
- [x] Generate `application-design/component-methods.md` with method signatures, input/output types, and purposes; defer detailed business rules to Functional Design.
- [x] Generate `application-design/services.md` with service responsibilities and orchestration patterns.
- [x] Generate `application-design/component-dependency.md` with a dependency matrix, communication patterns, and data flow.
- [x] Generate `application-design/application-design.md` consolidating the component design.
- [x] Validate consistency with requirements, stories, technical constraints, and applicable limited Security Baseline practices; validate Markdown, diagrams, links, and interface naming.

## Questions for the Team

Please answer every question. Choose a listed letter, or choose the final Other option and describe your answer after `[Answer]:`.

### Question 1: Agent and service boundary
Which high-level arrangement should coordinate the conversational agent, deterministic planning logic, and user interface?

A) Keep Streamlit as the UI/session boundary, a dedicated agent service for Ollama conversation and tool selection, and separate domain services for requirement mapping and plan validation (recommended)

B) Put conversation, retrieval, requirement mapping, and plan validation behind one application service, with internal modules but no distinct service interfaces

C) Use a different arrangement; describe the boundary and why it fits the local prototype

D) Other (please describe after [Answer]: tag below)

[Answer]: A. Keep Streamlit as the UI/session boundary, use a dedicated local agent service for Ollama conversation and tool selection, and keep requirement mapping and plan validation as separate in-process domain services. The separation is for the agent/application internals; the student sees their findings through the chat interface, not separate service interfaces.

### Question 2: Course and requirement data interfaces
How should components receive course, review, and curated program requirement data?

A) Give each approved source a dedicated adapter that returns validated, source-cited domain records; expose curated requirements through a separate read-only repository interface (recommended)

B) Use one unified data service that calls the approved sources and returns a common record shape, while retaining per-field source citations

C) Use a different interface; describe how source identity, missing data, and validation remain visible

D) Other (please describe after [Answer]: tag below)

[Answer]: A. Use dedicated adapters for each approved source and a separate read-only repository for curated program requirements. Curate requirements for every undergraduate program Cornell offers. Acquire the program requirements by web-scraping the Cornell Programs Catalog as described in the input documents. Validate scraped records and retain source attribution; expose missing, ambiguous, or stale coverage rather than treating it as certain. If the input documents do not make the program scope or scraping approach sufficiently clear, flag the ambiguity for the team.

### Question 3: Conversational session state
Where should the active student profile and evolving plan live during a session?

A) Keep session state in Streamlit session memory and pass typed snapshots to services; do not persist it or send it to external data sources (recommended)

B) Keep state in a dedicated in-memory session store behind an interface, with Streamlit holding only a session identifier

C) Use another session-state boundary; describe its lifetime and how it remains local and non-persistent

D) Other (please describe after [Answer]: tag below)

[Answer]: A. Keep student profile and evolving plan in Streamlit's active session memory, pass typed snapshots to services, and discard them when the session ends. Do not persist user information anywhere outside the current chat/session or send it to external services.

### Question 4: LLM tool boundary and safety
How should the local model interact with application capabilities?

A) Expose a fixed registry of narrowly scoped tools whose arguments use Pydantic schemas; tool handlers call only approved fixed-host adapters and return validated, cited results (recommended)

B) Use the same fixed tools and validation, but let the agent service own the source adapters directly rather than calling separate data services

C) Use another boundary; describe how tool arguments, endpoint allow-lists, and untrusted review/user text are contained

D) Other (please describe after [Answer]: tag below)

[Answer]: A. Use a fixed registry of narrowly scoped tools with Pydantic argument schemas. Handlers call only approved fixed-host adapters and return validated, cited results. Validate outputs and source coverage before presenting results; user trust is a key product differentiator.

### Question 5: Dependency and data-flow documentation
Which communication pattern should the design prefer between components?

A) Direct Python interfaces with typed request/response models and explicit dependency injection; no message broker or network service layer (recommended)

B) A small event/callback interface for conversation updates, with typed direct calls for data retrieval and plan validation

C) Another pattern; describe why it is needed for this local, single-user prototype

D) Other (please describe after [Answer]: tag below)

[Answer]: A. Use direct Python interfaces with typed request/response models and explicit dependency injection; no message broker or network service layer. We may revisit this choice depending on the project timeline.

## Answer Analysis and Remaining Clarification

- Questions 1, 3, 4, and 5 select a consistent local, in-process design. The internal services remain behind the chat UI; only their findings and citations are user-facing. Session-only handling agrees with the approved privacy requirements. Validation reinforces the approved grounding and plan-validity requirements.
- Question 2 selects separate source adapters and a curated requirements repository. The team specifies coverage for every Cornell undergraduate program and web-scraping the Cornell Programs Catalog, consistent with the Vision and Technical Environment input documents.
- Catalog coverage and refresh clarification from the team: use the catalog's table of all currently offered undergraduate programs as the coverage inventory. Scrape requirements intermittently between semester transitions because requirements rarely change mid-semester. Catalog pages may retain legacy remarks while updates are being implemented; preserve and surface those remarks as source context or uncertainty rather than silently treating them as current requirements. The design should record retrieval timing, validate scraped program coverage against the table, and mark gaps or potentially transitional content for review.
- Follow-up clarification: requirement text must be structured for downstream mapping and validation. Request a straightforward human review every time the catalog is re-scraped before accepting the updated curated snapshot; no heavyweight review process is needed.
- The direct-call approach in Question 5 is the current decision and may be revisited if project timeline constraints change; that note does not block the present design plan.

## Approval Gate

The team explicitly approved this plan on 2026-11-21. The generated design artifacts, including structured requirements, per-scrape human review, and locally derived sentiment scores for CU Reviews text, were explicitly approved on 2026-11-21. Application Design is complete.
