# Requirements: Course AIdvisor

## Intent Analysis

- **User request**: Develop the requirements document from the completed Vision and Technical Environment documents using the AI-DLC requirements analysis stage.
- **Request type**: New project requirements definition (greenfield).
- **Scope estimate**: System-wide product requirements spanning a local chat interface, agent orchestration, and public academic data sources.
- **Complexity estimate**: Complex. The product combines multi-turn advising, external data retrieval, requirement mapping, plan generation, validation, and LLM evaluation.
- **Requirements depth**: Comprehensive, based on the multiple user journeys, integrations, and measurable accuracy and privacy requirements already documented by the team.

## Summary

Course AIdvisor is a locally run conversational academic planning assistant for Cornell undergraduate students. It gathers a student's goals, academic background, and constraints; retrieves course, review, and program information; and produces cited recommendations and plans. It is an informational prototype, not an official degree audit or registration system. The provided Vision and Technical Environment documents are the source of the scope and technical decisions below.

## Stakeholders and Users

- **Primary users**: First- and second-year Cornell undergraduates planning future semesters, and third- and fourth-year students checking remaining requirements and planning toward graduation.
- **Supplemental stakeholders**: Cornell academic advisors, who may use the product as a preparation aid; the product must direct students to an advisor for decisions it cannot verify.
- **Project team**: Five student developers building and running the prototype locally.

## Functional Requirements

### Student Profile and Conversation

- **FR-01** The system shall conduct a conversational intake for a student's major, minors, interests, career goals, completed courses, prior knowledge, and course preferences.
- **FR-02** The system shall ask follow-up questions when information needed for a recommendation is missing or ambiguous.
- **FR-03** The system shall accept constraints including credit load, preferred time of day, and maximum review difficulty where the available source data supports them.
- **FR-04** When prior knowledge is unclear for a course sequence, the system shall be able to offer a multiple-choice placement quiz of 10–15 questions, designed to take about 10 minutes for someone experienced with the subject, to assess the student's familiarity with relevant subjects and explain how the result informs its suggested starting point.
- **FR-05** Student profile and plan data shall remain in the active local application session and shall not persist across sessions.

### Course Discovery and Reviews

- **FR-06** The system shall retrieve course offerings from the Cornell Class Roster API for a specified semester and use retrieved data to filter or describe courses by subject, level, credits, semester, and time of day when those fields are available.
- **FR-07** Course descriptions shall include relevant retrieved details such as title, description, credits, instructor, meeting information, prerequisites, forbidden overlaps, and recent or typical offering terms when available. When instructor information is returned by the Cornell Class Roster API, the system shall display it to the student with the course information.
- **FR-08** The system shall retrieve CU Reviews course identifiers and review data through the approved public endpoints and summarize available quality, difficulty, workload, and instructor information.
- **FR-09** The system shall support ranking or filtering recommendations using review constraints, such as a maximum difficulty, when the source data is available.
- **FR-10** If an external source is unavailable or lacks the requested data, the system shall disclose the limitation and shall not fabricate a course, rating, offering, or requirement.

### Requirements and Planning

- **FR-11** The system shall use Cornell undergraduate major and minor requirements from the Cornell Programs Catalog, represented as curated requirement data where needed, to map courses to program requirements.
- **FR-12** The system shall compare a student's entered completed courses and proposed courses with the selected program requirements and identify requirements represented as satisfied and remaining.
- **FR-13** The system shall generate a semester-by-semester plan for up to four semesters (two years) for one or more majors and several minors, subject to available source data and the student's constraints.
- **FR-14** The system shall flag prerequisite problems and forbidden course overlaps and shall not represent a plan as validated when such issues remain unresolved.
- **FR-15** The system shall explain the rationale for each recommendation, including relevant student goals, constraints, requirement coverage, course information, and review data when available.
- **FR-16** The system shall allow conversational plan refinement, including swapping a course, changing a constraint, and asking why a course was recommended, and shall re-check the revised plan.
- **FR-17** The system may extend planning beyond four semesters, up to eight semesters, only if feasible; the four-semester plan is the defined MVP baseline.

### Grounding, Safety, and User Experience

- **FR-18** Every factual claim about a course, requirement, or rating shall be tied to retrieved or curated source data and include a source citation.
- **FR-19** The system shall label information it cannot verify as unverified.
- **FR-20** For out-of-scope requests, including official exception decisions or unavailable syllabi, the system shall state that it cannot answer and provide a link to Cornell academic advising.
- **FR-21** The system shall provide a local web chat interface through which a student can complete the three MVP journeys: first-year planning, upperclassman requirement checking, and course exploration with an advising handoff.
- **FR-22** The system shall not enroll students, check real-time seat availability, provide an official degree audit, or claim to make official advising decisions.

## Non-Functional Requirements

### Performance and Quality

- **NFR-01 Grounding**: 100% of courses named in a response shall exist in retrieved roster data.
- **NFR-02 Plan validity**: At least 95% of evaluated plans shall have no prerequisite violations or forbidden overlaps and shall satisfy the requirements represented in the plan.
- **NFR-03 Citations**: 100% of factual claims about courses, requirements, and ratings shall include a source.
- **NFR-04 Tool use**: The agent shall select and call the appropriate tool for at least 95% of evaluated test queries.
- **NFR-05 Responsiveness**: On a typical team laptop, simple lookups shall complete in under 30 seconds and full multi-semester plans in under 180 seconds.
- **NFR-06 Usability**: At least four of five teammates or test students shall complete a full advising session without help.

### Privacy and Security

- **NFR-07 Local operation**: The application, student profile, and plan shall run locally. No student academic information shall be sent to a hosted LLM, cloud service, or other external service.
- **NFR-08 Data minimization**: The application shall not request or store names, NetIDs, Cornell credentials, or passwords. Students enter academic information manually; transcript import is out of scope.
- **NFR-09 Session handling**: Student profile and plan data shall remain in memory for the active session and be discarded when the session ends. The product shall have no accounts or cross-session profile storage.
- **NFR-10 Network security**: Requests to approved public Cornell and CU Reviews endpoints shall use HTTPS with certificate verification enabled. The local Ollama service shall not be exposed to the network.
- **NFR-11 Secrets**: The MVP shall require no secrets. Local configuration, if needed, shall be kept in a git-ignored environment file; credentials and keys shall not be committed or logged.
- **NFR-12 Prototype security**: Apply standard prototype security practices, including validated tool inputs, fixed allow-listed endpoint hosts, safe handling of untrusted review text and user input, and tests for prompt injection and secret exposure. The security extension is enabled at the limited prototype-standard scope selected by the team.

### Maintainability and Testability

- **NFR-13 Test coverage**: Deterministic code shall target at least 80% line coverage and 70% branch coverage.
- **NFR-14 Test isolation**: Unit and integration tests shall mock external HTTP calls and the Ollama client; recorded fixtures shall contain public data only.
- **NFR-15 LLM evaluation**: Maintain at least 15 realistic scenarios mapped to grounding, citations, tool use, and out-of-scope handling metrics, evaluated with the local model.
- **NFR-16 Testing strategy**: Include unit, integration, and security tests. End-to-end and performance tests are conditional as described in the Technical Environment. Apply property-based tests selectively to functions where example-based tests are difficult to create; when the appropriate test style is uncertain, the team will decide before writing those tests.

## Technical Constraints and Interfaces

- Python 3.12 or later is the application language; Streamlit provides the local chat UI and Ollama provides local LLM access using `qwen3:8b` (with a larger local model as a possible fallback).
- Use `uv` for dependency management, with `pyproject.toml`, a committed `uv.lock`, and `.python-version` as specified in the Technical Environment.
- Use Pydantic for validation, `httpx` for HTTP requests, and `pytest` for tests. New libraries require approval by a majority of the five-person team and must not require API keys, paid tiers, or cloud accounts.
- Approved public data sources are the Cornell Class Roster API, CU Reviews `get-by-info` and `get-reviews` endpoints, and the Cornell Programs Catalog. Respect source rate limits and use only public, unauthenticated access.
- The application runs as local processes started from the command line. Cloud hosting, containers, Cornell login, authenticated data, and hosted LLM APIs are outside the MVP environment.

## MVP Scope Boundaries

In scope: conversational student profiling, placement quizzes, course discovery, course and instructor review summaries, undergraduate program requirement tracking, plans up to four semesters, conversational plan refinement, citations, and explicit handoff for unsupported questions.

Out of scope: Cornell login, transcript import, credentials, enrollment, registration, seat availability, detailed weekly timetable optimization, persistent accounts or plan history, other universities, and cloud deployment. The product provides informational planning assistance and is not an official degree audit.

## Open Decisions and Assumptions

The Vision document identifies the following product questions as unresolved; this requirements draft does not invent decisions for them:

1. **Planning horizon detail**: Whether semesters beyond the current/upcoming term should receive equally detailed plans or more tentative recommendations. The MVP baseline is four semesters, with offering and catalog data limits disclosed.
2. **Career goals versus requirements**: How to balance a student's desired job trajectory against completion of major and minor requirements. The MVP shall use career goals as a personalization signal while clearly showing requirement coverage; the team has not specified a priority rule for conflicts.
3. **Student struggling in a course**: How to respond when a student reports poor performance. The product shall avoid making unsupported academic judgments and shall hand off to a human advisor for decisions requiring individualized official guidance; the precise advising policy remains to be decided.

The four-semester limit is the MVP requirement; extension to eight semesters is optional and feasibility-dependent. Source availability and accuracy may limit requirement mapping or future-term offerings, so the application must expose those limits rather than imply official or guaranteed outcomes.

## Extension Configuration and Applicability

- **Security Baseline**: Limited scope enabled, interpreted as standard prototype security practices. Applicable here to local data handling, public endpoint constraints, input validation, and prompt-injection considerations; implementation-specific checks remain for later design and code stages.
- **Resiliency Baseline**: Disabled by the team for this prototype; not applied.
- **Property-Based Testing**: Partial scope selected. Apply selectively where example-based tests are difficult; the team's specific uncertainty rule is retained. PBT-02, PBT-03, PBT-07, PBT-08, and PBT-09 are the configured blocking rules when applicable during later testing/design stages; detailed applicability will be assessed then.

## Requirements Acceptance Criteria

The requirements are ready for approval when the team confirms that this document accurately represents the intended MVP. In later implementation review, the measurable success criteria above will be assessed with test scenarios and usability sessions. No implementation or workflow planning is authorized by this document alone.
