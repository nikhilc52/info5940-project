# Application Design: Course AIdvisor

## Design Summary

Course AIdvisor is a local Streamlit chat application backed by a local Ollama agent, fixed source tools, and deterministic domain services. Components communicate with direct Python calls and typed Pydantic models. The agent's service boundaries are internal; students see cited findings, plan status, uncertainty, and advising handoffs through chat.

## Architectural Decisions

1. Separate internal responsibilities: Streamlit owns UI and active session state; the agent owns conversation and fixed tool selection; requirement mapping and plan validation are separate in-process domain services.
2. Dedicated source adapters: Cornell Roster and CU Reviews each have fixed-host adapters. Cornell catalog requirements are obtained by scraping public catalog pages, structured into requirement records, and exposed through a separate read-only curated repository.
3. Complete program inventory: Use the catalog table of currently offered undergraduate programs as the coverage basis. Compare curated records to that inventory and disclose missing, ambiguous, or invalid entries.
4. Between-semester refresh: Scrape intermittently between semester transitions; record retrieval timing and source URLs. Validate and request a straightforward human review of every candidate snapshot before it replaces curated records.
5. Legacy catalog remarks: Preserve remarks with source context and flag them as potentially transitional during each re-scrape review. Do not silently interpret them as current requirements. Mark affected mappings uncertain if status cannot be established.
6. Privacy: Keep student profile, conversation, and plan only in active local session memory. Do not persist or log student information or send it to external services. Public API requests contain only minimum lookup parameters.
7. Trust and validation: Validate tool arguments and source data; use deterministic mapping and validation; attach citations to factual claims; disclose unavailable and uncertain data; never claim official audit or advising authority.
8. Direct calls: Use explicit dependency injection and typed Python interfaces, with no message broker or network service layer. This may be revisited if timeline constraints change.
9. Review sentiment: Provide a locally derived sentiment score from available CU Reviews text, with citations and review count. Keep it distinct from CU Reviews' source ratings; define the score scale and aggregation during Functional Design.

## Main Request Path

1. Streamlit collects a student message and reads the active session snapshot.
2. The agent sends conversation context to local Ollama and offers a fixed registry of tools.
3. Tool handlers validate arguments and call the planning orchestrator or source adapters.
4. The orchestrator fetches approved public data, computes review sentiment locally, reads curated program records, maps requirements, and validates plans.
5. Typed results return to the agent with provenance and issues. The agent explains without changing deterministic findings.
6. Streamlit renders the response, citations, uncertainty, and plan findings. Session state remains in memory.

## Catalog Maintenance Path

1. Scrape the Cornell catalog's undergraduate program table to establish expected program IDs.
2. Scrape listed program pages and structure the requirement text into program/requirement records; retain source URLs, retrieval timestamps, and legacy remarks.
3. Validate parsed records and compare coverage with the program table.
4. Present each re-scraped snapshot for a lightweight human review, especially missing/duplicate records, ambiguous requirements, and transitional remarks.
5. Update the curated public snapshot only after validation and that review; expose retrieval timing and coverage report to requirement mapping.

## Design Scope and Deferrals

This is a high-level component design. Detailed prerequisite parsing, course matching, plan generation algorithms, catalog selectors, refresh execution procedure, and validation thresholds belong in Functional Design and later construction. No new vendor, cloud service, user account, persistent student store, enrollment capability, or official degree-audit behavior is added.

## Extension Compliance for Application Design

- **Security Baseline (limited prototype-standard scope):** Compliant for this design artifact. It specifies Pydantic validation, fixed source hosts, HTTPS, local-only runtime, separation of validation and orchestration, safe handling of untrusted text, and no student-content persistence/logging. Authentication, cloud IAM, centralized log services, network intermediaries, deployment monitoring, and at-rest encryption are N/A to the approved local prototype architecture or deferred to implementation where relevant.
- **Resiliency Baseline:** N/A; disabled by team decision.
- **Property-Based Testing (partial):** PBT-02, PBT-03, PBT-07, PBT-08, and PBT-09 are the configured blocking rules; detailed test framework and test properties are addressed during Functional Design/NFR Requirements. This high-level Application Design stage does not select a test framework or prescribe generated tests. Other PBT rules are advisory under partial enforcement.

## Related Artifacts

- [Components](components.md)
- [Component methods](component-methods.md)
- [Services](services.md)
- [Component dependencies and data flow](component-dependency.md)
