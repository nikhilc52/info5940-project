# Component Methods: Course AIdvisor

Method signatures below are high-level contracts. Detailed matching algorithms, parsing rules, scheduling behavior, retries, and error policy are deferred to Functional Design. SourceResult[T] carries validated data or typed issues and applicable provenance.

## Shared Models

- SourceCitation: source_name, validated source URL, retrieval time, optional source excerpt.
- SourceResult[T]: optional data, citations, issues, and completeness indicator.
- SessionSnapshot: student profile, optional plan, and conversation turns.
- AssistantResponse: message, citations, uncertainty explanations, and optional plan findings.

Source URL validation is constrained to approved hosts in implementation. SessionSnapshot must not be serialized to persistent storage or logs.

## Streamlit UI and Session State

- render_chat() -> None: collect current message and render conversation.
- read_session_snapshot() -> SessionSnapshot: build a typed snapshot from local session memory only.
- render_response(response: AssistantResponse) -> None: display message, citations, uncertainty, and findings.
- clear_session() -> None: release profile and plan state when session ends or is reset.

## Agent Service and Tool Registry

- respond(snapshot: SessionSnapshot, message: str) -> AssistantResponse: call local Ollama, process bounded tool calls, and return cited typed response.
- list_tools() -> Sequence[ToolDefinition]: expose only fixed approved tool definitions.
- dispatch_tool(name: ToolName, arguments: Mapping[str, object]) -> ToolResult: validate tool and arguments, then dispatch; reject unknown tools and malformed arguments without network access.

## Source Adapters

- search_courses(query: CourseQuery) -> SourceResult[Sequence[CourseRecord]]: return validated course facts and citations from fixed Cornell Roster host.
- resolve_course(query: CourseIdentityQuery) -> SourceResult[CourseReviewIdentity]: resolve a course through the approved CU Reviews endpoint.
- get_reviews(identity: CourseReviewIdentity) -> SourceResult[ReviewSummaryInput]: return review inputs with provenance and untrusted-content metadata.
- score_reviews(reviews: Sequence[ReviewRecord]) -> ReviewSentimentResult: derive a locally computed sentiment score from available review text, with review count, citations, and an insufficient-data indicator.

Adapters return explicit issues for unavailable or incomplete data and do not fabricate records.
Sentiment is a derived measure, separate from source-provided quality/difficulty ratings. Its scale and aggregation method are deferred to Functional Design.

## Catalog Scraper and Repository

- scrape_program_index() -> SourceResult[Sequence[ProgramIndexEntry]]: parse the catalog table of currently offered undergraduate programs; retain citation and retrieval time.
- parse_program_page(entry: ProgramIndexEntry) -> SourceResult[ProgramRequirementRecord]: extract requirements into structured fields and retain legacy remarks with source location and retrieval time.
- validate_catalog_snapshot(snapshot: CatalogSnapshot) -> CatalogValidationReport: compare parsed programs with index and report gaps, duplicates, invalid records, and transitional remarks requiring review.
- get_program(program_id: ProgramId) -> ProgramRequirementRecord | None; list_programs() -> Sequence[ProgramRequirementRecord]; coverage_report() -> CatalogCoverageReport.

The repository exposes structured curated records and coverage status only. Refresh is intermittent between semester transitions. Each re-scraped candidate is validated and then presented for a straightforward human review before it becomes active. The review does not require a heavyweight approval workflow; it checks the new structured requirements and any flagged legacy remarks.

## Domain Services

- map_progress(request: RequirementMappingRequest) -> RequirementMappingResult: report apparent requirement status, evidence, citations, unmatched items, and uncertainty.
- validate_plan(request: PlanValidationRequest) -> PlanValidationResult: return known violations and status; unresolved issues prevent a validated status.
- build_or_refine_plan(request: PlanningRequest) -> PlanningResult: orchestrate approved adapters/repositories, mapping, and validation for a new or revised plan.

Results are informational, not official academic determinations.

## Dependency Injection

Construct adapters, repositories, local model client, clock, and service objects at application startup and inject them into the orchestrator and tool handlers. Boundaries use direct Python calls and typed models. No message broker, network service layer, arbitrary URL parameter, or student-data persistence adapter is included.
