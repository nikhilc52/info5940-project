# Technical Environment: Course AIdvisor Application

## Project Technical Summary

- **Project Name**: Course AIdvisor
- **Project Type**: Greenfield
- **Primary Runtime Environment**: Local only. The product runs on each team member's laptop (a Streamlit app plus a local Ollama server) and is not hosted on any server.
- **Cloud Provider**: None.
- **Target Deployment Model**: Local processes started from the command line. There are no containers, VMs, or serverless functions, and setup is a documented install and run command sequence.
- **Team Size**: 5 university student developers
- **Team Experience**: Very experienced with Python and basic web/API development. Limited prior experience with agentic LLM techniques (tool calling and chatbot interfaces), which we were introduced to in class.

## Programming Languages

### Required Languages

| Language | Version | Purpose | Rationale |
|----------|---------|---------|-----------|
| Python | 3.12+ | Entire application: Streamlit UI, agent loop, tool wrappers for the roster, CU Reviews, and catalog APIs, requirements encoding, and tests | Team expertise, first-class Ollama client and Streamlit support, and a single language keeps a 5-person team consistent |

### Permitted Languages

| Language | Conditions for Use |
|----------|-------------------|
| Bash/POSIX shell | Setup and run scripts only (for example, pulling the model and starting the app). Keep scripts short and documented |
| JSON/YAML | Data formats (not programming languages) for the test fixtures |


### Prohibited Languages

| Language | Reason |
|----------|--------|
| JavaScript / TypeScript | Streamlit removes the need for a separate frontend, and a second stack would add setup and review overhead for our small team |

## Frameworks and Libraries

### Package Manager

| Tool | Version | Purpose | Rationale |
|------|---------|---------|-----------|
| uv | Latest stable | Dependency management, virtual environments, Python version management, and running commands | Fast, one tool for installs and environments, and a lockfile gives every teammate identical dependencies on macOS, Windows, and Linux |

- **Dependency file**: `pyproject.toml` lists direct dependencies, with exact versions pinned. `uv.lock` is committed so every teammate gets the same environment.
- **Python version**: Python 3.12 is pinned in `.python-version`, and `uv` installs it if it is missing.
- **Setup**: `uv sync` creates `.venv` and installs everything.

### Required Frameworks

| Framework/Library | Version | Domain | Rationale |
|-------------------|---------|--------|-----------|
| Streamlit | 1.46 | Chat UI (`st.chat_message`, `st.chat_input`) | Pure-Python chat interface, so no separate frontend or API layer is needed to run locally |
| ollama (Python client) | 0.40 | LLM access | Official client for the local Ollama server, with native tool-calling support for `qwen3:8b` |
| Pydantic | 2.12 | Data models and validation | Validates API responses, tool arguments, and requirement files, which prevents bad data reaching the agent |
| httpx | 0.28 | HTTP client | One HTTP client for the roster, CU Reviews, and catalog calls, with timeouts and easy mocking in tests |
| pytest | 9.2 | Unit and integration testing | Standard Python test runner, with fixtures for mocked API responses |

### Preferred Libraries

| Library | Purpose | Use When |
|---------|---------|----------|
| Beautiful Soup 4 (with lxml) | HTML parsing | Extracting program requirements from the Cornell catalog pages |
| respx | Mocking httpx calls | Unit tests for API tool wrappers, so tests never call Cornell or CU Reviews |

### Prohibited Libraries

| Library | Reason | Alternative |
|---------|--------|-------------|
| requests, aiohttp | A second HTTP client would split mocking and error handling | httpx |
| Selenium, Playwright | Browser automation is slow, brittle | httpx with Beautiful Soup |
| OpenAI, Anthropic, or other hosted-LLM SDKs | Unplanned vendor lock-in or API key spending is not allowed | The `ollama` client |
| unittest (as the test runner), nose | Mixed test styles make tests harder to read and maintain | pytest |

### Library Approval Process

- Any library not on the required or preferred lists must be proposed in the team chat or at a team session with its purpose and why a listed library will not work.
- A majority of the five members approves it. The approval is recorded in the AI-DLC workflow, and the library is added to the dependency file with a pinned version.
- New libraries must not require API keys, paid tiers, or cloud accounts.

## Cloud Environment

### Cloud Provider
- **Primary Provider**: None. The product runs entirely locally and is not deployed to any cloud.
- **Account Structure**: Not applicable. No cloud accounts are created or required.
- **Regions**: Not applicable.

**How it runs locally**: A local web app built with Streamlit calls a Python agent loop on the same machine. The agent calls `qwen3:8b` through a local Ollama server and calls public Cornell Class Roster, CU Reviews, and Cornell Programs Catalog endpoints over HTTPS for course data. Nothing is hosted, and no incoming network connections are required.

### Service Allow List
Only these local components and external endpoints may be used without approval.

| Service | Approved Use Cases | Constraints |
|---------|-------------------|-------------|
| Ollama (local server) | Running `qwen3:8b` for conversation, tool calling, summarization, and planning | Runs on localhost only, with the model pulled in advance. Not exposed to the network |
| Streamlit (local server) | Chat UI | Runs on localhost only, started from the command line |
| Cornell Class Roster API | Live course data | Public, read-only, no login. Respect rate limits |
| CU Reviews endpoints (`get-by-info`, `get-reviews`) | Course and instructor ratings and reviews | Public, read-only, no login. |
| Cornell Programs Catalog | Source for program requirements | Public pages only. |

### Service Disallow List

| Service | Reason | Alternative |
|---------|--------|-------------|
| Any cloud provider (AWS, Azure, GCP) | Not needed, since the product only has to run locally, and it adds cost and setup | Local processes on a laptop |
| Hosted LLM APIs (OpenAI, Anthropic, Google, and similar) | Cost, API keys, and student data leaving the machine. | Ollama with a local model, possibly a larger one |
| Hosting and deployment platforms (Streamlit Community Cloud, Heroku, Vercel, Docker registries) | Hosting is out of scope | Run locally for demos |
| Services requiring a Cornell login or credentials | Out of scope and a security risk | None. Features that need a login are excluded |

### Service Approval Process

- Any service not on the allow list must be proposed in the team chat or at a team session with its purpose and why a listed service will not work.
- A majority of the five members approves it. The approval is recorded in the AI-DLC workflow.
- New services must not require API keys, paid tiers, or cloud accounts.

## Security Requirements

### Authentication and Authorization
- **Authentication Method**: None. The app runs locally for one user at a time, and no accounts or logins exist. It never asks for, accepts, or stores Cornell credentials.
- **Authorization Model**: Not applicable. All data the app reads is public, read-only, and fetched without a login.
- **Token Format**: Not applicable. No tokens are issued or stored.
- **Session Management**: One Streamlit session per browser tab. Student profile and plan live only in session memory and are discarded when the session ends. Nothing persists across sessions.

### Secrets Management
- **Secrets Storage**: The base MVP requires no secrets, since all endpoints are public and the model is local. Any configuration value (model name, Ollama URL) goes in a git-ignored `.env` file.
- **Rotation Policy**: Not applicable, since no secrets are used.
- **Access Policy**: Each developer uses their own local configuration. Keys are never shared in chat, issues, or commits.
- **Prohibited Practices**:
  - No secrets, keys, or credentials in source code, committed config, test fixtures
  - No Cornell passwords or NetIDs requested, stored, or logged
  - No shared credentials across teammates
  - No committed `.env` files (enforced by `.gitignore`)

### Data Protection
- **Encryption in Transit**: HTTPS (TLS 1.2+) for all calls to Cornell and CU Reviews endpoints, with certificate verification left on in `httpx`. Traffic between Streamlit, the agent, and Ollama stays on localhost.
- **PII Handling**: The only student-provided data is academic information (major, minors, interests, completed courses, career goals). Students are told not to enter names, NetIDs, or other identifiers. The app does not ask for them and does not log chat contents beyond the local console during development. No student data is sent to any external service, including any LLM provider.
- **Data Classification**: Course roster, review, and catalog data is Public. Student chat input is Confidential and stays in memory on the user's machine.

### Security Compliance Framework

- **Framework chosen**: OWASP Top 10 (2021), plus the OWASP Top 10 for LLM Applications as a supplement for the agent's prompt and tool risks.
- **Rationale**: The product is a web application (Streamlit) that calls external APIs and processes untrusted text (review content and user input) through an LLM that can call tools. OWASP Top 10 is the standard for web applications, and the LLM supplement covers risks specific to agents. Hence, both are appropriate for this project.

## Testing Requirements

### Test Strategy Overview
| Test Type | Required | Coverage Target | Tooling |
|-----------|----------|----------------|---------|
| Unit Tests | Yes | 80% line coverage minimum (deterministic code: tools, validators, etc.) | pytest |
| Integration Tests | Yes | Agent loop with a faked Ollama client and mocked HTTP APIs, covering every tool end to end | pytest |
| End-to-End Tests | Conditional | The three MVP user journeys, through the Streamlit UI | Streamlit `AppTest` (`streamlit.testing.v1`), plus a manual demo run with live Ollama |
| LLM Evaluation Tests | Yes | A scenario set of at least 15 realistic student scenarios, scored against the Key Success Metrics | pytest with the `live_llm` marker, run against local `qwen3:8b` |
| Contract Tests | No | Not applicable. There are no inter-service APIs. External API response shapes are checked with fixtures and Pydantic models | Pydantic |
| Performance Tests | Conditional | Response-time targets: lookups under 30 seconds, full plans under 180 seconds, on a typical team laptop | pytest timing checks (`live_llm`), measured manually before milestones |
| Security Tests | Yes | Prompt-injection, input-validation, and secret-scanning checks | pytest |

### Unit Testing Standards
- **Coverage Minimum**: 80% line coverage and 70% branch coverage for deterministic code (tool wrappers, validators, etc.). LLM output quality is measured in the evaluation tests, not by coverage.
- **Mocking Policy**: Mock external dependencies: the Ollama client and all HTTP calls to Cornell and CU Reviews. Do not mock internal logic such as the requirement matcher or plan validator.
- **Naming Convention**: Files are `test_<module>.py` and functions are `test_<unit>_<behavior>_<condition>`, for example `test_check_prerequisites_flags_missing_course`.
- **Test Location**: A separate `tests/` tree mirroring the source layout: `tests/unit/`, `tests/integration/`, `tests/e2e/`, `tests/llm_eval/`, and `tests/fixtures/` for recorded API responses and sample requirement files.

### Integration Testing Standards
- **Scope**: Run the real agent loop with a fake Ollama client that returns scripted tool calls. Verify that the right tool is called with valid arguments, that tool results are passed back correctly, and that the final answer cites its sources. Test failure handling: roster API timeout, CU Reviews unavailable, malformed responses, and unknown course codes.
- **Environment**: Local only. HTTP is mocked with `respx`, and Ollama is faked.
- **Data Management**: Fixtures in `tests/fixtures/` hold recorded roster responses, CU Reviews responses, and sample program requirements. Fixtures are version-controlled, contain only public data, and are refreshed deliberately, not automatically. Tests do not share mutable state.

### End-to-End Testing Standards
- **Scope**: The three MVP journeys only (first-year plan, upperclassman requirement check, course lookup with out-of-scope handoff).
- **Environment**: Streamlit `AppTest` with the fake Ollama client for automated runs. 
- **Data-testid Requirements**: Not applicable.

### LLM Evaluation Testing Standards
- **Scenario set**: At least 15 scenarios in `tests/llm_eval/scenarios.yaml`, each with a student profile, a question, and expected checks. Include the Information Science + Economics + Philosophy example, an upperclassman finishing a minor, a single-course lookup, and out-of-scope questions.
- **Checks (mapped to the Key Success Metrics)**:
  - Grounding: every course in a response exists in the mocked or retrieved roster data (target 100%)
  - Citations: every factual claim has a source (target 100%)
  - Tool use: the expected tools are called for each scenario (target at least 95%)
  - Out-of-scope handling: a "cannot answer" response with an advising link (target 100%)

### Security Testing Standards
- **Prompt injection**: At least one test feeds a review containing instructions such as "ignore previous instructions" and verifies the agent does not follow it, and does not call tools or change its behavior.
- **Input validation**: Tests that malformed or hostile tool arguments (invalid subject codes, oversized values, URL-like strings) are rejected by the Pydantic models and never reach `httpx`.

### Performance Testing Standards
- **Baseline Requirements**: Lookups under 30 seconds and full multi-semester plans under 180 seconds on a typical team laptop
- **Test Scenarios**: Timed runs of representative scenarios with `qwen3:8b`, recorded in the repository. No load testing, since the app serves one local user.
- **Tooling**: pytest timing assertions under the `live_llm` marker, and manual timing for demos.

### CI/CD Testing Gates
The project has no deployment pipeline. 

| Pipeline Stage | Required Tests | Failure Action |
|---------------|---------------|----------------|
| Pre-commit (local) | Unit tests | Fix before committing |
| Pre-demo (manual) | Smoke test: start Ollama and Streamlit, run each journey once | Fix before the demo |

## Example Code Patterns

### Typical Endpoint: Tool Wrapper

This "endpoint" is a tool wrapper: a function the LLM can call that validates its arguments, makes one request to a fixed allow-listed host, and returns validated, citable data. Every tool (roster, CU Reviews, catalog) follows this pattern.

The final product may have more features or different features than what is described here, this is just a baseline for the type of code/style of code that should be written.

```python
# src/aidvisor/tools/roster.py
import time
import httpx
from pydantic import BaseModel, Field, ValidationError, field_validator

ROSTER_BASE_URL = "https://classes.cornell.edu/api/2.0"  # fixed host; the model never picks URLs
MIN_INTERVAL_S = 1.0  # Roster API usage limit: no more than 1 request per second
_last_call = 0.0

class RosterError(Exception):
    """Raised when the roster cannot be reached or returns unusable data."""

class SearchCoursesArgs(BaseModel):
    """Validated arguments for the search_courses tool."""

    roster: str = Field(pattern=r"^(FA|WI|SP|SU)\d{2}$", description="Semester code, e.g. FA26")
    subject: str = Field(pattern=r"^[A-Za-z]{2,6}$", description="Subject code, e.g. INFO")
    max_credits: int | None = Field(default=None, ge=1, le=12)

    @field_validator("subject")
    @classmethod
    def _uppercase(cls, v: str) -> str:
        return v.upper()

class Course(BaseModel):
    code: str  # "INFO 1200"
    title: str
    prereq: str | None
    credits_max: float | None
    last_terms_offered: list[str]  # e.g. ["2026SP", "2025FA"]
    source_url: str  # every fact the agent states must cite a source

def search_courses(args: SearchCoursesArgs, client: httpx.Client | None = None) -> list[Course]:
    """Return classes for one subject and roster, filtered by the validated arguments."""
    global _last_call
    time.sleep(max(0.0, MIN_INTERVAL_S - (time.monotonic() - _last_call)))  # rate limit
    _last_call = time.monotonic()

    try:
        resp = (client or httpx).get(
            f"{ROSTER_BASE_URL}/search/classes.json",
            params={"roster": args.roster, "subject": args.subject},
            timeout=10.0,
        )
        resp.raise_for_status()
        payload = resp.json()
    except (httpx.HTTPError, ValueError) as exc:
        raise RosterError(f"Roster request failed: {exc}") from exc
    if payload.get("status") != "success":
        raise RosterError(f"Roster returned status {payload.get('status')!r}")

    courses = []
    for raw in payload["data"]["classes"]:
        units = [g["unitsMaximum"] for g in raw.get("enrollGroups") or [] if g.get("unitsMaximum")]
        terms = raw.get("lastTermsOffered") or ""
        courses.append(Course(
            code=f'{raw["subject"]} {raw["catalogNbr"]}',
            title=raw.get("titleLong", ""),
            prereq=raw.get("catalogPrereq") or None,
            credits_max=max(units) if units else None,
            last_terms_offered=[t.strip() for t in terms.split(",") if t.strip()],
            source_url=f'https://classes.cornell.edu/browse/roster/{args.roster}'
                       f'/class/{raw["subject"]}/{raw["catalogNbr"]}',
        ))
    if args.max_credits is not None:  # credits are not an API parameter, so filter here
        courses = [c for c in courses if c.credits_max is None or c.credits_max <= args.max_credits]
    return courses

# Schema the LLM sees (Ollama tool format), generated from the same Pydantic model
SEARCH_COURSES_TOOL = {
    "type": "function",
    "function": {
        "name": "search_courses",
        "description": "List Cornell classes for one subject in one semester, with credits, "
        "prerequisites, and recent terms offered.",
        "parameters": SearchCoursesArgs.model_json_schema(),
    },
}

def handle_search_courses(raw_args: dict) -> dict:
    """Entry point called by the agent loop. Errors go back to the model as data, not crashes."""
    try:
        courses = search_courses(SearchCoursesArgs.model_validate(raw_args))
    except ValidationError as exc:
        return {"error": f"Invalid arguments: {exc}"}
    except RosterError as exc:
        return {"error": str(exc), "hint": "Tell the student the roster is unavailable; do not guess."}
    return {"courses": [c.model_dump() for c in courses]}
```

**Pattern notes**
- Failures return as data so the agent can recover or tell the student, instead of crashing or making something up.
- The `httpx.Client` can be injected, which lets tests mock the API with `respx`.
- Each result carries a `source_url` for the citation rule.

### Typical Function: Deterministic Plan Checker

Plan validity is checked by ordinary code, never by trusting the model. This pure function has no network, no LLM, and no side effects, so it is easy to test.

```python
# src/aidvisor/planning/validator.py
from typing import Literal

from pydantic import BaseModel


class Violation(BaseModel):
    kind: Literal["missing_prereq", "forbidden_overlap"]
    course: str
    detail: str


def check_plan(
    semesters: list[list[str]],
    prereqs: dict[str, set[str]],
    forbidden_overlaps: set[frozenset[str]],
    completed: set[str] | None = None,
) -> list[Violation]:
    """Return every prerequisite and forbidden-overlap violation in an ordered plan.

    A prerequisite must be completed in an EARLIER semester (or before the plan starts).
    """
    taken = set(completed or ())
    violations: list[Violation] = []
    for term in semesters:
        for course in term:
            missing = prereqs.get(course, set()) - taken
            if missing:
                violations.append(
                    Violation(kind="missing_prereq", course=course,
                              detail=f"requires {', '.join(sorted(missing))} first")
                )
            # compare with earlier courses and later courses in this term, so each pair is reported once
            for other in sorted(taken | {c for c in term if c > course}):
                if frozenset((course, other)) in forbidden_overlaps:
                    violations.append(
                        Violation(kind="forbidden_overlap", course=course,
                                  detail=f"forbidden overlap with {other}")
                    )
        taken |= set(term)
    return violations
```

**Pattern notes**
- Typed inputs and outputs, with results as data (`Violation`) so the agent can explain problems to the student.
- Prerequisites arrive as structured data (`dict[str, set[str]]`). The roster returns prerequisites as free text, so how that text becomes structured data is a Construction decision.

### Typical Test: Tool Wrapper with Mocked HTTP

```python
# tests/unit/test_roster.py
import httpx
import pytest
import respx

from aidvisor.tools import roster
from aidvisor.tools.roster import (
    ROSTER_BASE_URL, RosterError, SearchCoursesArgs, handle_search_courses, search_courses,
)

# Trimmed copy of a recorded response. Replace with a real recording in tests/fixtures/.
FIXTURE = {
    "status": "success",
    "data": {"classes": [{
        "subject": "INFO", "catalogNbr": "1200", "titleLong": "Information Ethics, Law, and Policy",
        "description": "Explores ethics of information technology.",
        "catalogPrereq": "", "catalogCoreq": "", "lastTermsOffered": "2026SP, 2025FA",
        "enrollGroups": [{"unitsMaximum": 3}],
    }]},
}
URL = f"{ROSTER_BASE_URL}/search/classes.json"


@pytest.fixture(autouse=True)
def no_rate_limit_delay(monkeypatch):
    monkeypatch.setattr(roster, "MIN_INTERVAL_S", 0)  # keep tests fast


@respx.mock
def test_search_courses_parses_fields_and_source_url():
    route = respx.get(URL).mock(return_value=httpx.Response(200, json=FIXTURE))

    courses = search_courses(SearchCoursesArgs(roster="FA26", subject="info"))

    assert route.calls.last.request.url.params["subject"] == "INFO"  # normalized to uppercase
    assert courses[0].code == "INFO 1200"
    assert courses[0].last_terms_offered == ["2026SP", "2025FA"]
    assert courses[0].source_url.endswith("/roster/FA26/class/INFO/1200")


@respx.mock
def test_search_courses_filters_by_max_credits():
    respx.get(URL).mock(return_value=httpx.Response(200, json=FIXTURE))
    assert search_courses(SearchCoursesArgs(roster="FA26", subject="INFO", max_credits=2)) == []


@respx.mock
def test_search_courses_timeout_raises_roster_error():
    respx.get(URL).mock(side_effect=httpx.ConnectTimeout("timed out"))
    with pytest.raises(RosterError):
        search_courses(SearchCoursesArgs(roster="FA26", subject="INFO"))


def test_handler_rejects_invalid_arguments_without_calling_network():
    result = handle_search_courses({"roster": "Fall 2026", "subject": "INFO"})  # no mock: a call would fail
    assert "Invalid arguments" in result["error"]
```

**Pattern notes**
- `respx` mocks all HTTP, so tests never contact Cornell, and the autouse fixture removes the 1-second rate-limit delay.
- Tests cover the happy path, a filter, a network failure, and bad model input, which are the four behaviors every tool wrapper should have tests for.