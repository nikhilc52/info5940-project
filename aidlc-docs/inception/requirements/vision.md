# Vision: Course AIdvisor Application

## Problem Statement

Planning courses today means filtering the course roster by hand, checking prerequisites and forbidden overlaps, tracking major and minor requirements, and looking up CU Reviews ratings one course at a time. For a student pursuing a major plus one or two minors, building a multi-semester plan can take hours.

## Executive Summary

Course AIdvisor is a chat application that acts as a personal academic advisor for Cornell students. The student describes their major, minors, interests, career goals, and prior knowledge. The agent interviews them, retrieves real data through tool calls (Cornell Class Roster API, CU Reviews, and program requirements), and produces a cited, semester-by-semester course plan with a stated rationale for every recommendation. If a student's prior knowledge is unclear, it can give a short placement quiz to decide which course in a sequence they should start with.

---

The product is an agent, not a single-call chatbot. The LLM decides which tools to call and in what order, interprets their results, asks clarifying questions, and only then responds. Every course, requirement, and rating it states must come from retrieved data and be cited. Anything it cannot verify is stated as unverified, and questions outside its capabilities are redirected to Cornell academic advising.

### Business Drivers
- **Planning is slow and error-prone.** Students manually filter the course roster, check prerequisites and forbidden overlaps, track major and minor requirements, and look up reviews course by course. A multi-program plan can take hours.
- **Existing tools fall short.** Planners like courseplan.io don't advise, can't place students through prerequisite gaps, and still require manual review lookups. Our team has also seen inaccurate or outdated information in them.
- **Small local LLMs are now capable enough.** Open models like Qwen3 can call tools and hold multi-turn conversations on a laptop, which makes a free, private, grounded advisor feasible.
- **Advising demand is high.** Students often need help planning courses and are limited by how much time they can get with a human advisor. An always-available first pass lets advisors focus on complex cases. From personal experience on our team, advising meetings need to be booked weeks in advance, and can take anywhere from 30 minutes to an hour.

## Key Success Metrics

- Grounding: 100% of courses named in a response exist in the retrieved roster data. For students' academic plans, accuracy is the most important metric.
- Plan validity: at least 95% of generated plans have no prerequisite violations or forbidden overlaps, and satisfy the requirements the student asked for
- Citations: 100% of factual claims about courses, requirements, and ratings include a source
- Tool use: the agent correctly chooses and calls the right tool for at least 95% of test queries
- Responsiveness: simple lookups complete in under 30 seconds, and full multi-semester plans in under 180 seconds, on a typical team laptop. The AIdvisor should be significantly faster than manual planning by students.
- Out-of-scope handling: 100% of out-of-scope test questions get an explicit "cannot answer" response with an advising link, rather than a guess
- Usability: at least 4 of 5 teammates or test students complete a full advising session without help

For deterministic success metrics, we will include specific test cases to assess the criteria. For others, our team will test various scenarios to ensure correct behavior.

### Target Users and Stakeholders

| User Type | Description | Primary Need |
|-----------|-------------|--------------|
| First and Second-Year Cornell Students    | Undergraduate students at Cornell University | Looking to plan their future course enrollments, given some constraints (academic requirements or personal preferences) |
| Third and Fourth-Year Cornell Students    | Upperclassmen at Cornell University | Advice on taking steps towards graduation, finishing major requirements, picking up minors along the way, while developing a plan catered for their future career (research, corporate, etc.) |
| Academic Advisors (supplemental, not a true target audience) | Existing Academic Advisors at Cornell University | Advisors can use the site as a preparation tool to improve their own skills offering advice, for students who might prefer in-person schedule collaboration |

### Scalability and Growth
The path of least resistance for growth is to expand to other colleges or universities, assuming they have similar infrastructure for course information and reviews. Additionally, this type of scheduling problem is not unique to course planning, and this style of solution could be adapted to other domains: event scheduling, club recruitment (for college students), or even trip itinerary planning could incorporate similar solutions. 

## MVP Scope

### MVP Objective
AIdvisor interviews a student about their major, interests, and background, retrieves real course roster, review, and program-requirement data through tool calls, and produces a cited, multi-semester course plan with the reasoning behind each choice.

### MVP Feature Areas

#### Feature Area 1: Conversational Student Profiling and Placement
- **Description**: Learns who the student is and what they already know, through conversation instead of forms.
- **Key Capabilities**:
  - Interview for major, minors, interests, career goals, completed courses, and preferences (credit load, time of day, max difficulty)
  - Short placement quizzes that place the student into the right course in a sequence
- **User Value**: Recommendations fit the student's actual background and goals, not a generic template.


#### Feature Area 2: Course Discovery and Intelligence
- **Description**: Finds, filters, and explains courses using live roster data.
- **Key Capabilities**:
  - Filter by subject, level, credits, semester, and time of day
  - Plain-language summaries tied to the student's interests, including typical semesters offered
  - Prerequisite and forbidden-overlap warnings
- **User Value**: Replaces hours of manual roster searching with accurate answers in seconds.


#### Feature Area 3: Reviews and Sentiment Insights
- **Description**: Turns CU Reviews data into usable guidance for courses and instructors.
- **Key Capabilities**:
  - Quality, difficulty, and workload metrics per course and instructor
  - Sentiment summaries of individual student reviews
  - Filtering and ranking plans by review-based constraints (for example, difficulty below 4.0)
- **User Value**: Students see what past students actually experienced without reading reviews one by one.

#### Feature Area 4: Program and Requirement Tracking
- **Description**: Maps courses to major, minor, and distribution requirements.
- **Key Capabilities**:
  - Access requirements for majors and minors at Cornell (for undergrad)
  - Shows which requirements each planned course satisfies and which remain
- **User Value**: Students stay on track for every program they pursue without missing a requirement.

#### Feature Area 5: Multi-Semester Planning and Refinement
- **Description**: Builds and adjusts full course plans across semesters.
- **Key Capabilities**:
  - Plans of up to 4 semesters (two years) for one or more majors and several minors
  - Ability to look ahead and see patterns in course offerings between Fall/Spring
  - Conversational edits (swap a course, change a constraint, ask why) with automatic re-validation
- **User Value**: A personalized, valid plan the student can adjust until it feels right.

#### Feature Area 6: Grounding, Trust, and Advising Handoff
- **Description**: Makes the advisor reliable enough to depend on.
- **Key Capabilities**:
  - Every course, requirement, and rating is tied to a tool result and cited
  - Unverified claims are labeled as unverified
  - Out-of-scope questions get a clear "cannot answer" and a link to Cornell academic advising
- **User Value**: Students can trust the output and know when to talk to a human advisor, if needed.

### Integration Points
The MVP state AIdvisor integrates with the following external systems and data sources.

- **Cornell Class Roster API** (`https://classes.cornell.edu/content/FA26/api-details`) - Source of live course data: titles, descriptions, credits, meeting times, instructors, prerequisites, forbidden overlaps, and semester offerings. Queried per semester (for example, FA26) for filtering and plan building.
- **CU Reviews: Get By Info** (`https://www.cureviews.org/api/courses/get-by-info`) - Looks up a course by subject and number, to resolve the course identifier needed to fetch its reviews.
- **CU Reviews: Get Reviews** (`https://www.cureviews.org/api/courses/get-reviews`) - Retrieves student reviews and ratings for a course, used for quality, difficulty, and workload metrics and for review sentiment summaries.
- **Cornell Programs Catalog** (`https://catalog.cornell.edu/programs/`) - Source of major and minor requirements, used to check which courses satisfy which program requirements.
- **Qwen3 via Ollama** (`https://ollama.com/library/qwen3`) - Local LLM that provides conversation, tool calling, summarization, and plan reasoning. The agent calls it through Ollama's local API, with a larger local model as a fallback if needed.
- **Streamlit** - Local web chat interface for the student, running on the user's machine.
- **Python backend** - Agent orchestration layer that connects the UI, the Ollama model, and the tool wrappers for the roster, CU Reviews, and catalog APIs.

### User Journeys (MVP)

#### Journey 1: First-Year Student Builds a Two-Year Plan
1. A first-year Information Science student opens the chat and says they want a data science career, plan to minor in Economics and Philosophy, know some R, and have no Python.
2. The agent interviews them about completed courses from HS, credit load, time-of-day preference, and a maximum CU Reviews difficulty of 4.0.
3. Because their Python background is unclear, the agent offers a 3-5 question placement quiz and uses the result to choose the starting programming course.
4. The agent calls the roster, CU Reviews, and program-requirement tools, then filters out courses that fail the student's constraints or have prerequisite problems and forbidden overlaps.
5. The agent returns a projected four-semester plan, with each course tied to the requirement it satisfies, its rating, and a cited rationale.
6. The student asks to swap a course, and the agent revises the plan and rechecks it.

**Outcome**: The student has a valid, personalized plan with sources they can verify, in minutes rather than hours.

#### Journey 2: Upperclassman Checks Requirements and Finishes a Minor
1. A junior states their major and minor and lists completed courses, since the MVP has no transcript import.
2. The agent asks about career direction (research or industry) and workload limits.
3. The agent maps the completed courses to program requirements and identifies which remain unmet.
4. The agent recommends possible plans to pick up an additional minor along the way to graduation. 
5. It recommends courses for the next semesters that cover the remaining requirements first, then ranks the options by interest and reviews.
6. The student asks why a particular course was chosen, and the agent explains using the retrieved roster data and ratings.

**Outcome**: The student knows exactly which requirements are left and has a short, justified list of courses to take next.

#### Journey 3: Student Explores a Course and Hits the Edge of the Product
1. A student asks about a specific course: what it covers, when it is offered, its prerequisites, and what past students think of the course and its instructors.
2. The agent retrieves the roster and review data and returns a summary with metrics and a sentiment overview, with citations.
3. The student then asks something the agent cannot answer, such as whether an exception to a prerequisite is allowed, or about syllabi.
4. The agent says plainly that it cannot answer this, labels any unverified information as such, and links to Cornell academic advising.

**Outcome**: The student gets trustworthy course information and a clear handoff to a human advisor when the product's limits are reached.

## Features In Scope (MVP)

| Feature | Description | Priority | Rationale for Inclusion |
|---------|-------------|----------|------------------------|
| Student profile interview | Conversational intake of major, minors, interests, career goals, completed courses, and constraints (credit load, time-of-day preference, maximum difficulty), kept for the length of the session | Must Have | Every personalized recommendation depends on knowing the student's goals and constraints |
| Course summarizing | Explains what a course covers, which semesters it is typically offered, and how it connects to the student's interests, based on roster data | Must Have | The core advising output; students need to understand courses before choosing them |
| Prerequisites and forbidden overlaps | Flags required courses and knowledge, and forbidden overlaps, so the student knows about conflicts before choosing | Must Have | Invalid plans are the main failure that students want to avoid |
| Course filtering | Filters roster offerings by credits, subject, level, semester, and time of day through parameterized roster API calls | Must Have | Replaces manual roster searching and gives the agent its main retrieval tool |
| Course and instructor reviews | Retrieves CU Reviews metrics (quality, difficulty, workload) for a course and its instructors, and summarizes sentiment from individual reviews | Must Have | Review-based constraints are a key differentiator from existing planners |
| Program requirement fulfillment | Determines which courses count toward a major or minor, using a curated set of requirements for all undergraduate programs listed in Cornell's catalog | Must Have | Plans must satisfy real degree requirements to be useful |
| Placement quizzes | Generates a short quiz (3-5 questions) on a foundational concept to place the student into the right course in a sequence, with the reasoning shown | Must Have | Handles unclear prior knowledge, a gap that existing tools do not address |
| Multi-semester planning | Generates a plan for up to 4 semesters (2 years) for one or more majors plus several minors, with each recommendation justified by retrieved data | Must Have | The central deliverable of the product (may extend to 8 semesters, if possible) |
| Plan refinement | Lets the student adjust the plan in conversation (swap a course, change constraints, ask why) and re-checks the result | Should Have | Students rarely accept a first plan unchanged |
| Grounding and citations | Every course, requirement, and rating comes from a tool result and is cited with its source; unverifiable claims are labeled as unverified | Must Have | Accuracy is the most important success metric for academic plans |
| Out-of-scope handling | When a question falls outside the product's capabilities, says so plainly and links to Cornell academic advising | Must Have | Prevents guessing and gives students a safe handoff to human advisors |
| Local chat interface | A web chat UI that runs on a laptop and talks to an Ollama instance | Must Have | The only way for users to interact with the product in the demo |

## Features Explicitly Out of Scope (MVP)

| Feature | Reason for Deferral | Target Phase |
|---------|-------------------|--------------|
| Cornell login features (official course evaluation data, course syllabi) | Requires authenticated access and handling of credentials, with security risk | Phase 3 (later in AI-DLC, but beyond the scope of this project) |
| Student transcripts | Requires login and sensitive records; students type in completed courses instead | Phase 3 |
| Storing or asking for student passwords or credentials | Unsafe for a student-built LLM product | Never |
| Rate My Professors and similar sites | Block automated access | Never |
| Enrollment, registration, or real-time seat availability | Requires authenticated, transactional access to Cornell systems | Never |
| Detailed weekly timetable optimization | Adds a complex scheduling problem beyond course selection | Phase 3 |
| User accounts, persistent profiles across sessions, and saved plan history | Adds storage and privacy work; the session is in-memory for the MVP | Phase 3 |
| Support for other universities | Needs new data sources and requirement formats for each school | Phase 3 |
| Hosting or cloud deployment | The product only needs to run locally | Never |

### MVP Limitations

- **Accepted Limitation**: There is no Cornell login, transcript import, or degree audit. This avoids handling credentials and sensitive records, at the cost of students entering their history manually.
- **Accepted Limitation**: The product runs locally with no accounts or saved plans across sessions. This keeps privacy and setup simple, but students must restart each session.
- **Accepted Limitation**: The advisor does not enroll students, check seat availability, or optimize weekly timetables. It recommends courses but does not manage registration.
- **Accepted Limitation**: Advice is informational and not an official degree check. The product always points students to Cornell academic advising for official decisions.

## Open Questions

- Should the course advisor provide detailed plans for each semester throughout the remaining years, or only a detailed plan for the upcoming/current semester and then a vague plan for the following semesters? (Having a detailed plan so many years in advance may lead to inaccuracies from flawed assumptions).
- When recommending courses, how should the Course AIdvisor take into account the student’s desired job trajectory or career plan alongside their intended major and/or minor(s)? (Taking into account the desired job trajectory may make the schedule more personal, but the Course AIdvisor should prioritize ensuring the student gets their necessary classes done first.)
- How should the course plan change if a student is doing poorly in a class? Should the AI encourage the student to continue down a tough course schedule, make things slightly easier, or offload the advising to a human academic advisor?
