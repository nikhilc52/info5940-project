# Story-to-Unit Map: Course AIdvisor

## Mapping Rules

Every story is supported by the full dependency chain for the capabilities it uses. The table lists the primary units that implement or deliver each story outcome; shared contracts and public data in Unit 1 underpin downstream stories even where not repeated in the “Primary units” column. All stories flow through Unit 3 and Unit 4 when they require conversational interaction and student-facing presentation. Privacy story J1-S6 is primarily delivered by Unit 4 and enforced across Units 1 and 3.

## Journey 1: First-Year Student Builds a Two-Year Plan

| Story | Outcome | Primary units | Coverage note |
|---|---|---|---|
| J1-S1 | Capture academic context and supported preferences | Units 3, 4 | Agent asks focused follow-ups; UI holds active session state. |
| J1-S2 | Provide placement quiz support and explain uncertainty | Units 2, 3, 4 | Planning capability supports placement evidence; agent and UI conduct the interaction and explain limits. |
| J1-S3 | Discover courses and compare sourced course/review information | Units 1, 2, 3, 4 | Roster and review adapters supply evidence; sentiment analysis is local and labeled as derived. |
| J1-S4 | Show cited course and requirement coverage in a four-semester plan | Units 1, 2, 3, 4 | Catalog records, mapping, validation, orchestration, explanation, and presentation are all needed. |
| J1-S5 | Refine and recheck a plan | Units 2, 3, 4 | Domain planning rechecks changed constraints; agent explains the result and UI maintains the active plan. |
| J1-S6 | Keep academic planning private and local | Units 1, 3, 4 | UI session state is not persisted; agent uses local Ollama; source adapters receive only minimal public lookup parameters. |

## Journey 2: Upperclassman Checks Requirements and Finishes a Minor

| Story | Outcome | Primary units | Coverage note |
|---|---|---|---|
| J2-S1 | Compare completed courses with major/minor requirements | Units 1, 2, 3, 4 | Structured curated requirements and source provenance feed mapping and cited summaries. |
| J2-S2 | Explore an additional minor within the available horizon | Units 1, 2, 3, 4 | Mapping and plan assembly use the catalog and course data; limitations and advising handoff remain visible. |
| J2-S3 | Prioritize courses by requirement coverage, interests, and supported constraints | Units 1, 2, 3, 4 | Source adapters and deterministic planning provide grounded comparisons and validation findings. |
| J2-S4 | Understand a recommendation and revise options | Units 2, 3, 4 | Revisions use planning services; agent explains cited trade-offs and UI captures the request. |
| J2-S5 | Explore a possible Economics major change | Units 1, 2, 3, 4 | Economics requirements and roster data support informational feasibility; unsupported eligibility decisions get a handoff. |
| J2-S6 | Check ORIE requirements before graduation | Units 1, 2, 3, 4 | ORIE mapping and feasible next steps remain informational, cited, uncertainty-aware, and distinct from official clearance. |

## Journey 3: Student Explores a Course and Hits the Edge of the Product

| Story | Outcome | Primary units | Coverage note |
|---|---|---|---|
| J3-S1 | Explore a course with cited details and reviews | Units 1, 2, 3, 4 | Course/review adapters and sentiment scoring return sourced facts and explicit missing-data signals. |
| J3-S2 | Connect course options to interests and career direction | Units 3, 4 | Agent asks follow-ups and explains options without unsupported career claims. |
| J3-S3 | Hand off unsupported or official decisions | Units 3, 4 | Agent identifies scope limits and UI shows the advising handoff and citations. |

## Coverage Check

- **Story count:** 15 stories mapped (J1-S1 through J1-S6; J2-S1 through J2-S6; J3-S1 through J3-S3).
- **No unmapped stories:** Every approved story ID in `stories.md` appears exactly once in the map.
- **Shared capability coverage:** Unit 1 contracts, provenance, and public records support relevant downstream paths; Unit 3 and Unit 4 provide the conversational and presentation layer across the product.
- **Trust and privacy:** Citation, uncertainty, validation, and local-session requirements are represented in the affected unit boundaries and integration chain.

## Extension Compliance

- **Security Baseline (limited prototype scope):** Compliant. Story coverage respects local student state, fixed source boundaries, citations, validation, and uncertainty.
- **Resiliency Baseline:** N/A; disabled by team decision.
- **Property-Based Testing (partial):** N/A for story mapping; test design will be addressed in applicable construction stages.
