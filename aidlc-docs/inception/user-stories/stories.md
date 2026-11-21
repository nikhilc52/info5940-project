# Course AIdvisor User Stories

> **Status:** Generated from the explicitly approved story-generation plan; awaiting review and explicit approval.

Stories are grouped by the three approved MVP journeys and labeled with a primary student persona. The sophomore considering Economics and the ORIE senior now have explicit story coverage within the requirement-checking journey. Each story describes a small student outcome, uses Given/When/Then acceptance scenarios, and traces to approved requirements. Thresholds are retained when the requirements define them. Course, rating, and requirement facts must remain grounded in retrieved or curated sources.

## Journey 1: First-Year Student Builds a Two-Year Plan

**Primary persona:** Incoming Freshman — Information Science, Coding Background.

### J1-S1 — Share academic background and preferences

As an incoming student, I want to describe my academic background, goals, and preferences conversationally so that recommendations reflect my situation.

**Requirements:** FR-01, FR-02, FR-03, FR-05, NFR-07, NFR-08, NFR-09.

**Acceptance criteria:**

- **Given** the student starts a session and has not provided a needed detail, **when** the assistant begins intake, **then** it asks focused follow-up questions for major, minors, interests, career goals, completed courses, prior knowledge, and supported preferences before relying on missing information.
- **Given** the student provides a credit-load, time-of-day, or maximum-difficulty constraint, **when** the assistant records the active profile, **then** it uses that constraint only when corresponding source data supports it and explains any unsupported constraint.
- **Given** the student ends the local application session, **when** the session is discarded, **then** profile and plan data are not retained for a later session.

### J1-S2 — Assess placement in a course sequence

As a student with uncertain preparation, I want a short placement quiz so that my suggested starting course reflects what I know.

**Requirements:** FR-04, FR-15, FR-18.

**Acceptance criteria:**

- **Given** prior knowledge is unclear for a course sequence, **when** the assistant proposes placement support, **then** it offers a 10–15 question multiple-choice quiz designed to take about 10 minutes for someone experienced with the subject.
- **Given** the student completes the quiz, **when** the assistant recommends a starting point, **then** it explains how the result informed that recommendation and cites any course facts used.
- **Given** the student declines or cannot complete the quiz, **when** placement remains uncertain, **then** the assistant asks for relevant background or labels the suggested placement as uncertain rather than presenting it as verified.

### J1-S3 — Discover courses that fit interests and constraints

As a student exploring data science, I want relevant courses filtered by my interests and constraints so that I can compare realistic options.

**Requirements:** FR-06, FR-07, FR-08, FR-09, FR-10, FR-18, FR-19.

**Acceptance criteria:**

- **Given** a requested semester and course interests, **when** the assistant searches the roster, **then** each named course exists in the retrieved roster data and available course details include source citations.
- **Given** roster and CU Reviews data are available, **when** the assistant compares options, **then** it reports available quality, difficulty, workload, and instructor review information and applies a maximum difficulty filter when supported.
- **Given** a source is unavailable or a requested field is absent, **when** the assistant responds, **then** it discloses that limitation and does not fabricate an offering, rating, instructor, or course fact.

### J1-S4 — See course and requirement coverage in a four-semester plan

As a first-year student pursuing multiple programs, I want a cited plan that connects courses to program requirements so that I can see a plausible path forward.

**Requirements:** FR-11, FR-12, FR-13, FR-14, FR-15, FR-18, NFR-01, NFR-02, NFR-03.

**Acceptance criteria:**

- **Given** the student provides completed courses and selected major or minors, **when** the assistant maps program progress, **then** it distinguishes requirements represented as satisfied from those remaining and cites the curated requirement source.
- **Given** sufficient source data and student constraints, **when** the assistant generates an MVP plan, **then** it covers no more than four semesters and explains each recommendation using relevant goals, constraints, requirement coverage, course information, and available review data.
- **Given** a proposed plan contains unresolved prerequisite or forbidden-overlap issues, **when** the plan is presented, **then** the assistant flags them and does not label the plan validated.
- **Given** an evaluated plan is counted against the approved plan-validity target, **when** validity is measured, **then** success requires at least 95% of evaluated plans to have no prerequisite violations or forbidden overlaps and satisfy represented requirements.

### J1-S5 — Refine and recheck a plan

As a student, I want to change a course or constraint and ask why an option was chosen so that I can adjust the plan while understanding the trade-offs.

**Requirements:** FR-15, FR-16, FR-18, FR-19.

**Acceptance criteria:**

- **Given** an existing plan, **when** the student swaps a course or changes a supported constraint, **then** the assistant updates the plan and rechecks requirement coverage, prerequisites, and forbidden overlaps.
- **Given** the student asks why a course was recommended, **when** the assistant explains, **then** it ties the rationale to the student's stated goals, constraints, requirement coverage, and available cited source data.

### J1-S6 — Keep academic planning private and local

As a student sharing academic history, I want my profile and plan to stay within my active local session so that I can use the assistant without account or cloud storage.

**Requirements:** FR-05, NFR-07, NFR-08, NFR-09, NFR-10, NFR-11.

**Acceptance criteria:**

- **Given** a student enters academic information, **when** the assistant uses the local model and approved public data sources, **then** academic profile and plan information are not sent to a hosted LLM or external service.
- **Given** a student starts using the product, **when** profile details are requested, **then** the assistant does not request a name, NetID, Cornell credentials, or password.
- **Given** the active session ends, **when** its state is cleared, **then** the profile and plan are discarded and are not available as cross-session history.

## Journey 2: Upperclassman Checks Requirements and Finishes a Minor

**Primary persona:** Junior — Statistics Major Considering an Economics Minor.

### J2-S1 — Enter completed courses and inspect requirements remaining

As an upperclassman, I want to enter completed courses and compare them with my major and minor requirements so that I can understand what remains.

**Requirements:** FR-01, FR-02, FR-11, FR-12, FR-18, FR-19.

**Acceptance criteria:**

- **Given** the student manually lists completed courses and selected programs, **when** the assistant maps progress, **then** it shows which represented requirements are satisfied and which remain, with citations to curated program data.
- **Given** a course or requirement cannot be matched confidently to source data, **when** progress is summarized, **then** the assistant labels it unverified and asks for clarification or identifies the limitation.

### J2-S2 — Explore adding a minor within the remaining horizon

As an upperclassman, I want to explore an additional minor alongside my current programs so that I can understand whether a course path may fit before graduation.

**Requirements:** FR-11, FR-12, FR-13, FR-17, FR-18, FR-20.

**Acceptance criteria:**

- **Given** the student selects an additional minor and provides completed courses, **when** the assistant explores a plan, **then** it maps available requirements and proposes no more than four semesters in the MVP baseline.
- **Given** the student asks to extend beyond four semesters, **when** the assistant considers up to eight semesters, **then** it does so only if feasible and clearly identifies data limits and uncertainty.
- **Given** the assistant cannot establish feasibility from available source data, **when** it responds, **then** it says what it cannot verify and directs official or exception decisions to Cornell academic advising.

### J2-S3 — Prioritize remaining requirements and constraints

As an upperclassman, I want candidate courses compared by requirement coverage, interests, and review constraints so that I can choose among next steps.

**Requirements:** FR-03, FR-06, FR-08, FR-09, FR-12, FR-14, FR-15, FR-18.

**Acceptance criteria:**

- **Given** multiple courses can address a remaining requirement, **when** the assistant presents options, **then** it explains requirement coverage and uses stated interests and supported review constraints to compare them.
- **Given** a candidate has a prerequisite problem, forbidden overlap, or unavailable offering data, **when** the option is presented, **then** the issue or limitation is explicit and the option is not represented as validated.

### J2-S4 — Understand rationale and adjust options

As an upperclassman, I want to ask why a course was chosen and request a revision so that I can weigh its fit against other priorities.

**Requirements:** FR-15, FR-16, FR-18, FR-19.

**Acceptance criteria:**

- **Given** the assistant has recommended a course, **when** the student asks why, **then** the response explains the connection to requirements, stated career direction or interests, constraints, and available cited course or review data.
- **Given** the student changes a constraint or asks to swap a course, **when** the assistant revises the options, **then** it rechecks the affected requirement and scheduling constraints and discloses any unverified information.

### J2-S5 — Explore a possible change to Economics

As a non-technical sophomore considering an Economics major, I want to compare my completed courses with represented Economics requirements and see what additional coursework may be needed so that I can assess possible next steps before deciding whether to change majors.

**Requirements:** FR-01, FR-02, FR-11, FR-12, FR-13, FR-15, FR-18, FR-19, FR-20, FR-22.

**Acceptance criteria:**

- **Given** the student has not provided their current major, completed courses, or college, **when** they ask whether changing to Economics is feasible, **then** the assistant asks for the missing information needed to compare their progress with available Economics requirements and does not assume a technical background.
- **Given** the student supplies completed courses and the product has curated Economics requirements, **when** the assistant compares the programs, **then** it identifies represented requirements as apparently satisfied or remaining, cites the requirement source, and labels course matches or policy details it cannot verify.
- **Given** the student requests a possible path forward, **when** available requirements and course data are sufficient, **then** the assistant outlines up to four semesters of relevant courses, explains the requirement coverage and assumptions, and flags unavailable offerings or unresolved prerequisites and overlaps.
- **Given** the student asks whether Cornell will approve the major change or whether they are officially eligible, **when** the product cannot make that determination, **then** it states that feasibility is informational rather than an approval decision and provides a Cornell academic advising link.

### J2-S6 — Check ORIE requirements before graduation

As a senior majoring in Operations Research and Information Engineering (ORIE), I want to compare my completed courses with represented ORIE requirements and identify apparent gaps so that I can prepare focused questions before seeking official graduation clearance.

**Requirements:** FR-01, FR-02, FR-11, FR-12, FR-13, FR-14, FR-15, FR-18, FR-19, FR-20, FR-22, NFR-01, NFR-02, NFR-03.

**Acceptance criteria:**

- **Given** the senior enters completed ORIE coursework, **when** the assistant maps progress against available curated ORIE requirements, **then** it lists requirements apparently satisfied and remaining with citations for every factual requirement claim.
- **Given** a course may satisfy multiple requirements, have a substitution, or cannot be matched confidently, **when** progress is summarized, **then** the assistant flags the uncertain mapping and does not count it as verified satisfaction.
- **Given** one or more apparent requirements remain and sufficient source data exists, **when** the assistant proposes next steps, **then** it prioritizes feasible courses within the remaining horizon, checks known prerequisites and forbidden overlaps, and states when offering or policy data is unavailable.
- **Given** the senior asks whether they have officially completed the ORIE major or will graduate, **when** the assistant responds, **then** it does not issue a degree audit or graduation decision and directs the student to Cornell academic advising for official confirmation.

## Journey 3: Student Explores a Course and Hits the Edge of the Product

**Primary persona:** Undergraduate — Information Science Major and Cognitive Science Minor Exploring Career Direction.

### J3-S1 — Explore an unfamiliar course and its reviews

As a student exploring career paths, I want a cited course and review summary so that I can compare academic options before choosing a direction.

**Requirements:** FR-01, FR-02, FR-06, FR-07, FR-08, FR-10, FR-15, FR-18, FR-19.

**Acceptance criteria:**

- **Given** the student asks about a course, **when** roster and review records are available, **then** the assistant summarizes relevant returned details such as title, description, credits, instructor, meeting information, prerequisites, typical offerings, and available quality, difficulty, workload, or sentiment information.
- **Given** factual course, requirement, or rating claims appear in the answer, **when** citations are checked, **then** 100% of those claims include a retrieved or curated source.
- **Given** review or roster information is missing, **when** the assistant summarizes the course, **then** it identifies missing data and does not infer a rating or offering as fact.

### J3-S2 — Connect course exploration to interests and goals

As a student unsure about career paths, I want follow-up questions and explanations that relate course options to my interests so that I can explore without receiving a generic list.

**Requirements:** FR-01, FR-02, FR-03, FR-15, FR-18.

**Acceptance criteria:**

- **Given** the student's career direction is unclear, **when** course recommendations depend on that context, **then** the assistant asks a focused follow-up and treats career goals as a personalization signal while showing requirement coverage separately.
- **Given** the student has not chosen between paths such as research and industry, **when** options are compared, **then** the assistant can explain how each option relates to stated interests without claiming an official or guaranteed career outcome.

### J3-S3 — Receive a clear handoff for unsupported decisions

As a student, I want the assistant to identify questions outside its reliable scope so that I know when to consult Cornell academic advising.

**Requirements:** FR-10, FR-19, FR-20, FR-22, NFR-03.

**Acceptance criteria:**

- **Given** the student asks for an official exception decision or unavailable syllabus, **when** the assistant cannot verify an answer from approved sources, **then** it explicitly says it cannot answer and provides a Cornell academic advising link.
- **Given** the student asks about enrollment, real-time seats, or an official degree audit, **when** the assistant responds, **then** it identifies that capability as out of scope and does not imply it performed the action or made an official decision.
- **Given** an advisor reads the student-facing journey, **when** they review the response, **then** the same citations, uncertainty labels, and handoff are visible; no separate advisor-only workflow is implied.

## Cross-Journey Quality Targets

These targets apply to evaluating the product across the journey stories; they are approved requirement targets, not per-response promises unless stated.

| Measure | Acceptance target | Requirement |
|---|---|---|
| Course grounding | 100% of courses named in a response exist in retrieved roster data | NFR-01 |
| Plan validity | At least 95% of evaluated plans meet represented requirements and have no prerequisite violations or forbidden overlaps | NFR-02 |
| Citations | 100% of factual claims about courses, requirements, and ratings include a source | NFR-03 |
| Tool selection | Appropriate tool selected and called for at least 95% of evaluated test queries | NFR-04 |
| Responsiveness | Simple lookups under 30 seconds; full multi-semester plans under 180 seconds on a typical team laptop | NFR-05 |
| Usability | At least 4 of 5 teammates or test students complete a full advising session without help | NFR-06 |
| Journey coverage | All five personas have at least one explicitly mapped, testable story in the story set | FR-21 |

## Scope Boundary

These stories cover conversational profiling, placement support, course and review exploration, requirement tracking, plans up to four semesters, plan refinement, citations, privacy, and advising handoff for freshman planning, minor planning, career exploration, a possible Economics major change, and ORIE graduation preparation. They do not add transcript import, enrollment, seat availability, official degree audits or decisions, cloud hosting, or persistent student accounts.


