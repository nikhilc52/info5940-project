# Services and Orchestration: Course AIdvisor

All services are in-process Python interfaces. Streamlit and local Ollama are local runtime endpoints; Cornell and CU Reviews are approved public read-only sources.

## Agent Service

- Purpose: Manage conversation and select fixed application tools through local Ollama.
- Inputs: Active session snapshot, current message, fixed tool definitions, and typed tool results.
- Outputs: Assistant response with explanation, citations, uncertainty, and any plan findings.
- Rules: Validate tool arguments with Pydantic; unknown tools fail closed; tools cannot choose hosts or arbitrary paths. Treat user messages and CU Reviews text as untrusted. The model may explain deterministic results but cannot override them.

## Course Discovery Orchestration

Call the roster adapter for the requested term; optionally resolve and retrieve CU Reviews data for returned courses; compute a locally derived sentiment score from available review text; combine only fields with provenance; return absent/unavailable fields as issues. Output typed candidates with citations, source ratings, sentiment score, review count, and explicit limitations. Label sentiment as derived and distinct from CU Reviews ratings.

## Program Catalog Ingestion and Repository

- Coverage: The catalog's table of currently offered undergraduate programs is the inventory. Compare every curated snapshot against it and report missing, duplicate, or invalid entries.
- Refresh: Scrape intermittently between semester transitions. Record retrieval timestamps and source URLs. Validate each candidate scrape and request a human review every time before updating the curated snapshot. Keep the review straightforward rather than introducing a formal multi-step approval process.
- Structured records: Convert requirement text into structured program records with identifiable requirement groupings and source provenance, so mapping and validation can consume the data predictably while retaining the catalog text and context.
- Legacy remarks: Preserve remarks with source context and flag as potentially transitional during the per-scrape human review. Do not silently interpret them as current requirements. If status cannot be established, mark affected requirements uncertain.
- Output: Read-only program records and a coverage report with inventory date, retrieval time, covered/missing programs, validation issues, and remarks requiring review.

## Requirement Mapping Service

Compare student-entered completed courses and proposed courses with selected program requirements. Return apparent satisfied/remaining status, evidence, citations, and unmatched/uncertain items. The service cannot issue substitutions, exceptions, major-change approvals, or degree completion decisions.

## Plan Validation Service

Independently check represented requirement coverage, known prerequisite ordering, forbidden overlaps, and approved constraints supported by validated data. Return findings with affected courses/requirements and evidence. Unknown/incomplete source data is reported separately from a passed check; unresolved violations prevent validated status.

## Planning Orchestrator

1. Receive a typed request from active session state and selected program/course goals.
2. Obtain curated program records and coverage report; request public roster/review data through source adapters as needed.
3. Call requirement mapping to identify represented gaps and candidate course coverage.
4. Assemble a proposed plan bounded to the approved four-semester MVP horizon. Any feasible optional extension is clearly labeled.
5. Call plan validation and return findings to the agent.
6. Agent explains results with citations and uncertainty; UI renders the answer.
7. Refinement repeats affected retrieval, mapping, and validation; student state stays in session memory.

## Failure and Uncertainty

- Adapters return typed source issues for timeouts, malformed records, missing fields, and unavailable sources.
- The orchestrator does not substitute guessed course or requirement facts.
- Partial catalog coverage limits mapping; missing programs cannot be presented as complete.
- Ambiguous legacy remarks are retained as context and flagged for review; affected claims are labeled uncertain.
- Deterministic findings are passed through; the agent cannot suppress a known conflict.
- Outputs remain informational, not official advising or degree-audit decisions.

## Privacy and Security Boundaries

- Student profile, completed courses, conversation, and plan remain in active local memory and are discarded at session end.
- Do not persist or log student content. Public calls receive minimum lookup parameters; no student identity or full conversation is transmitted.
- Requests use HTTPS with certificate verification and fixed approved hosts. Ollama and Streamlit bind to localhost.
- Validate tool arguments and source payloads. Treat user/source text as untrusted and endpoint configuration as outside model control.
