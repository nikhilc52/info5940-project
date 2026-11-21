# Unit Dependencies and Integration Sequence: Course AIdvisor

## Dependency Matrix

| Unit | Depends on | Provides to | Integration mode |
|---|---|---|---|
| Unit 1: Shared Contracts and Public Data Foundations | Pydantic, fixed approved source configuration, Cornell/CU Reviews/catalog public sources | Typed models, validated course and review records, structured curated requirements, provenance, source issues, coverage reports | Direct Python interfaces; read-only HTTPS at adapters only |
| Unit 2: Deterministic Planning and Review Analysis | Unit 1 contracts, roster/review records, curated requirement repository | Requirement mappings, plan validation findings, assembled/refined plans, derived sentiment results | Direct typed Python calls |
| Unit 3: Agent Orchestration and Fixed Tools | Units 1 and 2 contracts and services; local Ollama client | Validated fixed tool calls, conversation responses with citations and uncertainty | Direct typed Python calls; Ollama on localhost |
| Unit 4: Streamlit Experience and Session Integration | Unit 3 agent interface and shared response/session models from Unit 1 | Student chat workflow, active session state, rendered results and handoffs | Direct typed Python calls within one application process |

## Capability-Level Dependencies

- Unit 1 establishes types and public data contracts before consumers implement against them.
- Unit 2 consumes validated source data and catalog records. Its mapping, validation, and sentiment results are deterministic or locally derived and preserve source evidence.
- Unit 3 invokes approved services through fixed tools; it does not bypass adapters or replace deterministic findings.
- Unit 4 calls Unit 3 and renders returned evidence. It owns the only active student session state and does not send that state to public endpoints.

## Integration Sequence

1. **Unit 1 contracts and source access:** Freeze shared model shapes and provenance conventions, then implement source adapters and structured catalog repository against them.
2. **Unit 2 domain capabilities:** Integrate mapping, validation, planning orchestration, and sentiment with Unit 1 result types; exercise each capability against representative dependent story needs.
3. **Unit 3 agent and tools:** Register fixed tool schemas against the shared types and route calls to Units 1 and 2; verify evidence and uncertainty survive the response path.
4. **Unit 4 UI and session:** Connect the UI to the agent and render complete outputs; verify session-only handling and visible citations, limitations, and handoffs.
5. **Cross-unit story integration:** Walk each approved story through the relevant capability chain before treating the corresponding unit sequence as complete. Keep the team moving as one; this sequence does not assign parallel owners.

## Interface and Data Rules

- Use direct Python calls with shared Pydantic request/response models; do not add message brokers, service APIs, or inter-process calls between units.
- Source adapters alone issue read-only HTTPS requests, using fixed approved hosts and validated bounded parameters.
- Catalog ingestion creates structured public records with source URLs and retrieval times. Each candidate re-scrape receives a lightweight human review before replacing curated data.
- Student profile, conversation, and plan remain in active local session memory, never in persistent storage/logs and never in external source requests.
- Every factual course, requirement, or rating result retains citations; uncertainty and known validation findings pass through the complete chain.

## Textual Integration View

Unit 1 provides shared contracts and validated public records to Unit 2. Unit 2 provides deterministic planning and derived sentiment results to Unit 3. Unit 3 provides conversational responses to Unit 4. Unit 4 renders those responses to the student while keeping student state in local session memory. All units run within one local application process; Ollama is local and public source adapters access only approved HTTPS sources.

## Extension Compliance

- **Security Baseline (limited prototype scope):** Compliant. The dependency rules preserve fixed endpoints, data validation, local session privacy, and safe propagation of untrusted content.
- **Resiliency Baseline:** N/A; disabled by team decision.
- **Property-Based Testing (partial):** N/A for dependency sequencing; configured testing requirements apply in later construction work.
