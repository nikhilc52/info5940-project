# Unit of Work Plan: Course AIdvisor

## Context Loaded

- Approved requirements, personas, and user stories.
- Explicitly approved Application Design: one local Streamlit/Ollama application, direct Python interfaces, logical modules, dedicated public-source adapters, curated structured program requirements, requirement mapping, plan validation, and local review sentiment scoring.
- Greenfield Python 3.12+ project with a five-person team; no application code currently exists.
- Security Baseline is enabled at limited prototype-standard scope. Resiliency is disabled. Property-Based Testing is partially enabled; applicable testing details will be handled in later stages.

## Planning Scope and Initial Direction

Decompose the application into dependency-aware units of work while retaining one local deployment/process boundary. A provisional direction is to group work by capabilities and dependencies (shared models/data access, deterministic planning, agent/tool orchestration, and UI integration), with parallel ownership only where interfaces are stable. This is a starting point for the questions below, not an approved decomposition.

## Unit of Work Plan

- [x] Read approved requirements, stories, application design, and execution plan.
- [x] Resolve the unit decomposition questions below; analyze answers for ambiguity or conflict and add follow-up questions if needed.
- [x] Generate `application-design/unit-of-work.md` with unit definitions, responsibilities, boundaries, sequencing, and the greenfield code organization strategy.
- [x] Generate `application-design/unit-of-work-dependency.md` with a dependency matrix and integration sequence.
- [x] Generate `application-design/unit-of-work-story-map.md` mapping every approved story to one or more units.
- [x] Validate unit boundaries, dependencies, story coverage, code organization, and consistency with the approved single-process application design.
- [x] Obtain explicit approval of this unit-of-work plan before generating unit artifacts.

## Questions for the Team

Please answer each question after its `[Answer]:` tag. Choose a letter, or choose the final Other option and describe the answer.

### Question 1: Story grouping
How should stories be grouped into units of work?

A) Group by technical capability and dependencies (shared models/data adapters, deterministic planning, agent/tool orchestration, UI integration) (recommended)

B) Group around user journeys, with each unit delivering a vertical slice across UI, agent, and data access

C) Use a hybrid: build shared foundations first, then organize remaining work around journey slices

D) Other (please describe after [Answer]: tag below)

[Answer]: A — Group by technical capability and dependencies. Complete each capability across the user stories that depend on it before moving to the next capability.

### Question 2: Dependencies and integration
How should units coordinate shared interfaces and integration?

A) Define shared typed contracts early; complete dependency units first, then integrate through direct Python calls (recommended)

B) Let each unit define its own interfaces and reconcile them during a later integration unit

C) Keep one shared core unit and let dependent units integrate against its reviewed contracts

D) Other (please describe after [Answer]: tag below)

[Answer]: A — Establish a fixed, consistent structure and shared typed contracts early; complete dependency units first and integrate through direct Python calls.

### Question 3: Team alignment and ownership
How should the five-person team divide work across units?

A) Assign clear unit owners, with shared review/integration checkpoints and interfaces agreed by the team (recommended)

B) Pair on each unit and rotate ownership between units

C) Divide by existing team expertise; identify owners later when the unit boundaries are known

D) Other (please describe after [Answer]: tag below)

[Answer]: D — Leave team alignment and work division to the team’s existing rules; assume the team moves as one person.

### Question 4: Technical deployment boundary
Should the units remain modules in one local application?

A) Yes. All units are logical Python modules in one local application; no separately deployed services or network APIs (recommended and consistent with Application Design)

B) Keep a single application but allow a separate local process for selected components if a clear need is identified

C) Revisit the deployment boundary; describe the proposed change

D) Other (please describe after [Answer]: tag below)

[Answer]: A — Keep all units as logical Python modules in one local application, with no separately deployed services or network APIs.

### Question 5: Business-domain boundaries
Which capability boundaries should the decomposition preserve?

A) Keep source access, catalog requirements, deterministic mapping/validation, agent orchestration, and UI as distinct logical modules with typed interfaces (recommended)

B) Combine data retrieval and catalog requirements in one data module; keep planning logic, agent, and UI separate

C) Organize around student-facing capabilities and keep internal technical boundaries flexible

D) Other (please describe after [Answer]: tag below)

[Answer]: A — Keep separate module boundaries and access because individual modules interact with others in different ways; avoid oversized modules.

### Question 6: Greenfield code organization
Which repository structure should the units use?

A) A single `src/aidvisor/` Python package with modules grouped by capability and a `tests/` tree mirroring those modules (recommended)

B) A flat `aidvisor/` package at the repository root with a mirrored `tests/` tree

C) Another structure; describe the tradeoff for this local Python application

D) Other (please describe after [Answer]: tag below)

[Answer]: A — Use a single `src/aidvisor/` package grouped by capability with a mirrored `tests/` tree; this structure is plain and easy to follow.

## Approval Gate

After answers are complete and ambiguities are resolved, explicitly approve this unit-of-work plan before the unit artifacts are generated.

## Answer Analysis and Decomposition Decisions

- Q1 and Q5 agree: group work by technical capability, keep capabilities independently accessible, and complete the capabilities needed across dependent stories before moving on. This is consistent with user-story coverage and separate application-design modules.
- Q2 specifies fixed contracts and structure. Together with Q5’s preference for separate modules, this means stable interfaces and explicit dependency sequencing; no architectural conflict.
- Q3 delegates team division to the team’s existing rules and states to plan as one person. No ownership assignments or parallel-work assumptions will be included.
- Q4 confirms the approved local single-process boundary. Q6 selects the `src/aidvisor/` package and mirrored tests layout. Both align with the greenfield Python application design.
- No unresolved ambiguity or contradiction remains in the six answers.

The completed answers and analysis were explicitly approved by the user on 2026-11-21 before unit artifacts were generated.

