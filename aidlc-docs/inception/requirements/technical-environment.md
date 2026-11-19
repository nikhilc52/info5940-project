# Technical Environment: Cornell Course Advising LLM


## Language and Package Manager


- **Python 3.12+** for backend, integrations, filters, and LLM orchestration.
- **uv** to manage dependencies, create the development environment, and run tests.
- Use `pyproject.toml` for package/tool configuration; commit `uv.lock` for reproducibility.
- Use standard Python type hints and **Pydantic v2** models for input, source records, and response validation.


## Application Framework and User Interface


- **FastAPI** provides a local backend, typed request/response schemas, and OpenAPI endpoints.
- **Streamlit** provides a simple local chatbot interface and course-result tables, avoiding unnecessary front-end complexity for the MVP.
- **httpx** provides asynchronous HTTP requests for Cornell and permitted third-party data-source integrations.
- **Beautiful Soup 4** parses relevant, publicly accessible Cornell Program Catalog HTML pages.
- The app separates display, data acquisition, deterministic filtering, LLM orchestration, and evidence formatting into testable modules.


## LLM Provider, Model, and Agent Design


- **Provider:** Ollama, running locally on a team laptop.
- **Planned model:** `lfm3` (LFM3-1.7B), loaded through Ollama's local chat API at `http://localhost:11434`.
- **Why this choice:** This compact open model is intended for local-agent/tool-use and structured-output tasks; local execution avoids per-request hosted inference costs and supports an offline model runtime. It still needs network access to fetch current Cornell and review/catalog data.
- **Limitations:** LFM3's small size can struggle with complex multi-step reasoning. The application will not rely on the model to do exact timetable arithmetic, numerical comparisons, or official program-rule verification. The team must test tool-calling reliability early and can use a **deterministic orchestrator fallback** if model-driven tool selection is unreliable.


**Agent/tool flow:**


1. Parse student request into a validated intent object (term, subjects, level, constraints, interests, and requested sources).
2. Select and invoke approved tools such as `search_roster_courses`, `get_course_reviews`, `get_program_requirements`, and `check_schedule_conflicts`.
3. Normalize external responses and run exact filters, comparisons, and conflict calculations in Python.
4. Supply only relevant source excerpts, links, and filtered candidates to Ollama for explanation and summarization.
5. Validate the resulting answer against retrieved course identifiers and source availability. Include unknowns and source links rather than inventing information.


Restrict available tools, arguments, request counts, and domains. Set a small maximum tool-call iteration count and reject unsupported tool requests.


## Cloud Provider and Deployment Model


- **Cloud provider:** None required for MVP.
- **Deployment:** Local-only web application on team laptops; no AWS, cloud database, or public server.
- Run Ollama locally; start the FastAPI backend on `127.0.0.1:8000` and Streamlit on `127.0.0.1:8501`.
- The backend connects to the local Ollama service and retrieves public web/API information over HTTPS.
- A **small on-disk or in-memory cache** of public course/catalog metadata may speed repeat requests and reduce source load; the cache is not an authoritative source of current seat availability.
- A network connection is needed for fresh external data, although test fixtures can support integration testing without network access.


## External Data and Integration Contracts


| Tool or adapter | Source | Intended behavior | Failure behavior |
| --- | --- | --- | --- |
| `search_roster_courses` | `https://classes.cornell.edu/api/2.0/search/classes.json` | Fetch courses for a supported term and optional subject/search filters; normalize course and enrollment-group fields | Return a source-unavailable state; never invent a course |
| `get_course_reviews` | CUReviews public web/API source, contract to be validated | Retrieve available ratings/reviews for a course and, when supported, associated instructor | Return `reviews_unavailable`; do not assign a default difficulty score |
| `get_program_requirements` | `https://catalog.cornell.edu/programs/` and selected program pages | Retrieve permitted public HTML and extract relevant requirement text, catalog year, and URL | Return `requirements_unverified` if page or rule is unclear |
| `check_schedule_conflicts` | Normalized roster meetings and student-provided meetings | Compare meeting intervals on shared weekdays | Return `unknown` for TBA, missing or unparseable times |


**Cornell API requirements:** Follow official v2 API documentation and the stated **maximum one request per second**. For FA25 and newer rosters, parse separate `catalogPrereq` and `catalogCoreq` fields where provided instead of relying solely on the legacy `catalogPrereqCoreq`. Use `enrollGroup` and meeting-pattern data for timetable checks. Do not assume every course description is available or that all old fields remain populated.


**Third-party review integration:** The cited CUReviews URL has not yet established a complete, supported machine-readable contract. Confirm actual permitted endpoints, parameters, JSON shape, review attribution, rate limits, and usage conditions before implementing live requests. Keep this adapter replaceable; if an API is unavailable, disable live ratings or use explicitly labeled synthetic fixtures for tests, never present fake reviews as live data.


**Catalog retrieval:** Restrict requests to selected official `catalog.cornell.edu` URLs, respect site access restrictions and rate limits, cache responsibly, and store the page URL and relevant catalog-year context. A rules parser must not equate any casual mention of a course with confirmed program eligibility.


## Testing and Quality Assurance


- **pytest** for unit and integration tests; **pytest-cov** for coverage reporting (target **at least 80%** on deterministic service/integration logic).
- **pytest-asyncio** for async HTTP/service tests.
- **respx** for mocking httpx requests; use saved, sanitized fixtures to avoid unreliable network dependencies in tests.
- **ruff** for linting and formatting, **mypy** for type checking.
- **No AI-generated statement is accepted as a substitute for deterministic checks.** Test exact filtering and overlap logic separately from model-generated language.
- Add a small evaluation set of representative student requests. Check whether retrieved courses match constraints, whether answers cite retrieved sources, and whether the assistant appropriately states unknowns.


### Key Test Cases


- Undergraduate INFO search filters out courses above the maximum requested level.
- A course meeting from 10:00 to 11:15 on Monday conflicts with an existing Monday 11:00 to 12:00 meeting but not with a Tuesday-only meeting.
- Back-to-back classes ending and starting at the same minute are not marked as overlapping.
- An absent prerequisite field is treated as **unknown unless source semantics explicitly guarantee none**, not as evidence of no prerequisites.
- Courses without a CUReviews rating do not pass a strict difficulty-threshold filter as verified matches.
- A catalog page mentioning a course outside a qualifying-requirements context does not automatically make it eligible for a minor.
- HTTP errors, malformed upstream data, and Ollama timeouts produce user-friendly errors rather than fabricated recommendations.


## Prohibited Libraries and Practices


| Prohibited | Reason | Use Instead |
| --- | --- | --- |
| `eval()`, `exec()`, arbitrary execution of model-produced code | Security and correctness risk | Typed Pydantic tool arguments and explicit Python service functions |
| `requests` inside async request paths | Blocking I/O | `httpx.AsyncClient` |
| Flask or Django | Adds a second, unnecessary backend framework | FastAPI |
| `pip`, Poetry, Conda for project workflows | Inconsistent dependency environments | `uv` and a committed lockfile |
| Selenium or browser automation for ordinary catalog access | Unnecessary overhead and potentially intrusive site access | `httpx` plus Beautiful Soup on approved public pages |
| Unrestricted URL fetches from model output | SSRF and inappropriate crawling risk | Approved-host allowlist and validated URLs |
| Blind reliance on LLM output for numeric filtering/schedule checks | Non-deterministic accuracy | Tested Python filters and interval comparison |
| Hardcoded API secrets or user credentials | Privacy and security risk | Local `.env` excluded from Git; preferably avoid credentials altogether |
| Broad scraping or bypassing anti-bot controls | Reliability and permission concerns | Official APIs or explicitly permitted narrow retrieval |


## Security, Privacy, and Reliability Basics


- Bind local services to loopback interfaces; do not expose the backend or Ollama endpoint publicly by default.
- Use HTTPS for external data access, plus timeouts, bounded retries, source-level caching, and a Cornell API rate limiter.
- Restrict outgoing requests to approved external hosts. Validate course identifiers, subject codes, roster codes, URL paths, request lengths, and tool arguments.
- Treat all retrieved web content and student prompts as **untrusted input**. Ignore instructions embedded in retrieved data that ask the agent to change its behavior.
- Avoid Cornell NetID authentication, transcript uploads, and storage of sensitive academic records in MVP.
- Do not log raw prompts by default; redact unnecessary personal information from error logs.
- Prevent prompt-injection-driven tool use, infinite tool loops, and unbounded context by limiting calls and retrieved text length.
- Present retrieved facts with source URL and retrieval timestamp when practical; disclose partial source failures and catalog ambiguities.


## Example 1: Typical FastAPI Endpoint


The endpoint uses dependency injection so that the recommendation service can be mocked in tests. `AdvisorService` and `AdvisorAnswer` are application-specific types to be implemented during Construction.


```python
from typing import Annotated


from fastapi import APIRouter, Depends
from pydantic import BaseModel, Field


from cornell_advisor.services.advisor import AdvisorService, get_advisor_service
from cornell_advisor.models.responses import AdvisorAnswer


router = APIRouter(prefix="/v1")




class AdvisorRequest(BaseModel):
   question: str = Field(min_length=1, max_length=2000)
   roster: str = Field(default="FA26", pattern=r"^(FA|SP|SU|WI)\d{2}$")
   completed_courses: list[str] = Field(default_factory=list, max_length=40)




@router.post("/advise", response_model=AdvisorAnswer)
async def advise(
   request: AdvisorRequest,
   service: Annotated[AdvisorService, Depends(get_advisor_service)],
) -> AdvisorAnswer:
   return await service.answer(
       question=request.question,
       roster=request.roster,
       completed_courses=request.completed_courses,
   )
```


## Example 2: Deterministic Course-Filtering Function


This example represents only the strict numerical portion of course filtering; prerequisite and program-policy claims require the source-aware checks described above.


```python
from dataclasses import dataclass




@dataclass(frozen=True)
class CourseSummary:
   code: str
   level: int
   difficulty: float | None




def filter_courses(
   courses: list[CourseSummary],
   *,
   max_level_exclusive: int,
   max_difficulty_exclusive: float | None = None,
) -> list[CourseSummary]:
   """Return courses that satisfy all verifiable strict constraints."""
   matches: list[CourseSummary] = []
   for course in courses:
       if course.level >= max_level_exclusive:
           continue
       if max_difficulty_exclusive is not None:
           if course.difficulty is None:
               continue
           if course.difficulty >= max_difficulty_exclusive:
               continue
       matches.append(course)
   return matches
```


## Example 3: pytest Tests


```python
from cornell_advisor.services.filters import CourseSummary, filter_courses




def test_filters_course_level_and_difficulty() -> None:
   courses = [
       CourseSummary(code="INFO 2950", level=2000, difficulty=3.5),
       CourseSummary(code="INFO 5100", level=5000, difficulty=2.0),
       CourseSummary(code="INFO 3300", level=3000, difficulty=4.2),
   ]
   result = filter_courses(
       courses,
       max_level_exclusive=5000,
       max_difficulty_exclusive=4.0,
   )
   assert [course.code for course in result] == ["INFO 2950"]




def test_missing_rating_is_not_a_verified_match() -> None:
   courses = [CourseSummary(code="INFO 2040", level=2000, difficulty=None)]
   result = filter_courses(
       courses,
       max_level_exclusive=5000,
       max_difficulty_exclusive=4.0,
   )
   assert result == []
```


The example codes and numeric ratings above are **test fixtures only**, not claims about current Cornell offerings or actual CUReviews ratings.


## MVP Build Sequence


1. Establish repository, AI-DLC v1.0.1 rules, and required Inception documentation.
2. Implement normalized course models and Cornell roster integration, with rate limiting and mocked tests.
3. Build deterministic course filtering, basic schedule-conflict checking, and source metadata.
4. Integrate the selected catalog pages and a CUReviews adapter (or a documented unavailable-source fallback).
5. Connect Ollama LFM3 with constrained tool calls/intent parsing and evidence-grounded responses.
6. Add Streamlit chat/table interface, tests, evaluation prompts, and local demo instructions.


## Open Technical Decisions


- Validate CUReviews access method, response schema, usage permissions, and rating fields before committing to live integration.
- Benchmark LFM3 tool calls and structured responses on actual team laptops; prefer orchestration fallback if accuracy is insufficient.
- Choose a fixed catalog year and small initial program set for reliable requirement checks.
- Decide whether a memory-only cache suffices or a small local SQLite cache is warranted.
- Decide whether course ranking should be deterministic, LLM-generated with citations, or a clearly separated combination.


## Official References


- Cornell Course Roster API: https://classes.cornell.edu/content/FA26/api-details
- Cornell Program Catalog: https://catalog.cornell.edu/programs/
- CUReviews course example: https://www.cureviews.org/course/CS/2110
- Proposed Ollama model: https://ollama.com/library/lfm3
- Academic advising: https://www.undergrad.cornell.edu/students/advising/college-academic-advising



