# Application Components: Course AIdvisor

## Scope

The design describes local Python components and their interfaces. Services are in-process modules, not separately deployed servers. Students interact through Streamlit; internal service boundaries are not exposed as product features. Detailed matching, parsing, scheduling, and validation rules belong in Functional Design.

## Components

### Streamlit Chat UI and Session State

- Purpose: Provide the local chat experience and hold the active student's profile, conversation, and evolving plan.
- Responsibilities: Collect goals and supported constraints; ask for missing information; show cited results, uncertainty, and advising handoffs; clear state when the session ends.
- Interfaces: render_chat(); read_session_snapshot() -> SessionSnapshot; render_response(response: AssistantResponse) -> None.
- Boundary: Student profile and plan remain in active local session memory. Do not write them to files, logs, databases, or cross-session stores, and do not send them to external data services. Public queries contain only minimum lookup parameters.

### Agent Service and Fixed Tool Registry

- Purpose: Coordinate the local Ollama model, multi-turn conversation, and capability selection.
- Responsibilities: Build model context; offer a fixed registry of narrowly scoped tools; validate arguments with Pydantic; route calls through application services; return structured evidence; treat user and review text as untrusted.
- Interfaces: respond(snapshot: SessionSnapshot, message: str) -> AssistantResponse; list_tools() -> Sequence[ToolDefinition]; dispatch_tool(name: str, arguments: Mapping[str, object]) -> ToolResult.
- Boundary: Calls local Ollama only. It does not decide official academic eligibility, override deterministic findings, or invent missing facts.

### Cornell Roster Adapter

- Purpose: Retrieve course and term information from the approved Cornell Class Roster API.
- Responsibilities: Use fixed HTTPS host/path configuration, bounded query parameters, timeouts and rate limits; validate response shapes; produce typed records with provenance and source URLs.
- Interface: search_courses(query: CourseQuery) -> SourceResult[Sequence[CourseRecord]].

### CU Reviews Adapter

- Purpose: Retrieve public course identifiers and review data from approved endpoints.
- Responsibilities: Validate inputs and response shapes; mark review text as untrusted; preserve provenance; disclose unavailable or missing fields; provide review text to a local sentiment scorer.
- Interfaces: resolve_course(query: CourseIdentityQuery) -> SourceResult[CourseReviewIdentity]; get_reviews(identity: CourseReviewIdentity) -> SourceResult[ReviewSummaryInput].

### Review Sentiment Scorer

- Purpose: Produce a sentiment score from retrieved CU Reviews text for course comparison.
- Responsibilities: Score only available review text locally; return a bounded, typed score with review count and citations to source reviews; distinguish derived sentiment from CU Reviews' own ratings and disclose when there is insufficient text.
- Interface: score_reviews(reviews: Sequence[ReviewRecord]) -> ReviewSentimentResult.
- Boundary: Scoring method and aggregation are detailed in Functional Design. Do not send review text to a hosted service or present the derived score as an official CU Reviews rating.

### Cornell Catalog Scraper and Curated Program Repository

- Purpose: Maintain curated requirement records for every undergraduate program listed in the Cornell Programs Catalog.
- Responsibilities: Parse the catalog's undergraduate program table as the coverage inventory; extract program pages into structured requirement records; retain source URLs, retrieval time, and legacy remarks; validate records and compare repository coverage with the inventory; request a human review of each re-scraped catalog snapshot before accepting it. Keep that review lightweight because requirements are expected to be straightforward. Refresh intermittently between semester transitions.
- Interfaces: scrape_program_index() -> SourceResult[Sequence[ProgramIndexEntry]]; parse_program_page(entry: ProgramIndexEntry) -> SourceResult[ProgramRequirementRecord]; validate_catalog_snapshot(snapshot: CatalogSnapshot) -> CatalogValidationReport; repository get_program(program_id) -> ProgramRequirementRecord | None; list_programs() -> Sequence[ProgramRequirementRecord]; coverage_report() -> CatalogCoverageReport.
- Boundary: Repository is read-only to application services and contains curated public catalog data in a structured form suitable for mapping (program identity, requirement groupings, requirement text, source provenance, and any applicable remarks). It never stores student profiles or plans. Every re-scraped candidate is reviewed by a person before replacing the active curated snapshot. Legacy remarks remain attributed context and are not silently interpreted as current requirements.

### Requirement Mapping Service

- Purpose: Compare manually entered completed/proposed courses with selected curated program requirements.
- Responsibilities: Produce apparent satisfied/remaining mappings, evidence and citations; mark uncertain or unrepresented requirements; distinguish informational matching from an official degree audit.
- Interface: map_progress(request: RequirementMappingRequest) -> RequirementMappingResult.

### Plan Validation Service

- Purpose: Deterministically check a proposed semester plan against available course and requirement data.
- Responsibilities: Check represented coverage, known prerequisites, forbidden overlaps, and other approved constraints supported by validated data; return findings and evidence; never label unresolved violations as validated.
- Interface: validate_plan(request: PlanValidationRequest) -> PlanValidationResult.

### Planning Orchestrator

- Purpose: Assemble data and coordinate services for recommendations and refinement.
- Responsibilities: Request roster/review/catalog data; call mapping and validation; return typed evidence to the agent with source and uncertainty metadata.
- Interface: build_or_refine_plan(request: PlanningRequest) -> PlanningResult.

### Shared Domain Models and Provenance

Shared Pydantic types include SessionSnapshot, CourseQuery, CourseRecord, ProgramIndexEntry, ProgramRequirementRecord, SourceCitation, SourceResult[T], RequirementMappingRequest, RequirementMappingResult, PlanValidationRequest, PlanValidationResult, and AssistantResponse. Models validate shapes, bounds, identifiers, optionality, and provenance, and represent source failures and uncertainty as data.

## Cross-Cutting Constraints

- Local Ollama and Streamlit bind to localhost; only read-only HTTPS calls to approved Cornell and CU Reviews sources are external.
- Student profile and plan are session-only and excluded from logs and persistent stores.
- Every factual course, requirement, or rating claim carries a source citation; uncertain/unavailable facts are labeled.
- The tool registry and endpoint allow-list are fixed; model and untrusted text never supply arbitrary URLs or executable instructions.
- Catalog coverage is checked against its undergraduate program table. Retrieval time is recorded. Transitional legacy remarks are surfaced for review.
- The product is informational, not an official degree audit or advising decision service.
