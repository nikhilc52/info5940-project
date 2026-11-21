# User Stories Generation Plan: Course AIdvisor

## Context Loaded

- Approved requirements in `../requirements/requirements.md`.
- Product vision and user journeys in `../requirements/vision.md`.
- Technical constraints in `../requirements/technical-environment.md`.
- User Stories stage selected for execution in the approved `execution-plan.md`.

## Story Approach Options

| Approach | Benefits | Trade-offs |
|---|---|---|
| User journey-based | Follows end-to-end student tasks and the three documented journeys | Cross-cutting capabilities may appear in multiple journeys |
| Feature-based | Makes capability coverage easy to scan | Can lose the student's end-to-end context |
| Persona-based | Highlights differences between student groups | Similar capabilities may be repeated |
| Domain-based | Separates planning, catalog data, and advising boundaries | Less intuitive to validate as a student workflow |
| Epic-based | Shows broad outcomes and nested detail | Can become hierarchical overhead for an MVP |

## Proposed Method

Use a journey-led hybrid: group stories under the three approved user journeys, label each with the primary persona, and reference relevant requirements. Split stories when they represent independently valuable and testable outcomes. Keep deterministic plan validity, grounding, citations, and privacy as acceptance criteria where observable; avoid inventing implementation tasks or new product scope. Use Given/When/Then acceptance criteria for concrete behavior and include explicit source and limitation behavior where factual advice is involved.

## Generation Checklist

- [x] Review all user answers and resolve ambiguities before seeking plan approval.
- [x] Generate `stories.md` with INVEST-oriented stories and acceptance criteria; revise it after persona updates to add specific, testable Economics major-change and ORIE graduation-requirement stories.
- [x] Generate `personas.md` with user archetypes and characteristics.
- [x] Map personas to relevant stories and map stories to approved requirements, including explicit mappings for the sophomore and senior personas.
- [x] Check that stories cover the three MVP journeys and distinguish in-scope from out-of-scope behavior.
- [x] Validate Markdown structure, traceability, and consistency with approved requirements.

## Questions for the Team

Please fill every `[Answer]:` tag below. Use a listed letter, or choose the final Other option and describe the answer after the tag. These questions resolve story organization and observable acceptance behavior; they do not change approved product scope.

### Question 1: Story organization
Which organization should be used for the story set?

A) Journey-led hybrid: group by the three approved journeys, label the primary persona, and cross-reference requirements (recommended)

B) Feature-based: group by MVP capability and label the personas served

C) Persona-based: group by student archetype and label the journey supported

D) Other (please describe after [Answer]: tag below)

[Answer]: A. Journey-led hybrid, with journey groups labeled by persona and requirement links.

### Question 2: Story size
How should stories be sized for this MVP?

A) Small, independently valuable student outcomes, splitting a journey into multiple stories when outcomes can be accepted separately (recommended)

B) One larger story per end-to-end journey, with acceptance criteria covering each major step

C) Other (please describe after [Answer]: tag below)

[Answer]: A. Several small, independently valuable student outcomes per journey.

### Question 3: Supplemental advisor persona
How should academic advisors appear in the generated personas and stories?

A) Include advisors as a supplemental persona only for preparation and student handoff; student users remain primary (recommended)

B) Treat advisors as a co-primary persona with their own preparation workflow

C) Other (please describe after [Answer]: tag below)

[Answer]: C. Advisors are supplemental readers of the same student journeys; do not create a separate advisor persona or story.

### Question 4: Acceptance criteria style
Which acceptance criteria format should be used?

A) Given/When/Then scenarios for each story, with measurable thresholds retained where they apply (recommended)

B) Concise, testable bullet criteria, retaining measurable thresholds where they apply

C) Other (please describe after [Answer]: tag below)

[Answer]: A. Given/When/Then scenarios with measurable thresholds retained where applicable.

## Approval Gate

After answers are complete and any ambiguity is resolved, the team must explicitly approve this story-generation plan before stories or personas are generated.

## Generation Status

- Plan approval: explicitly received on 2026-11-21.
- Generation: complete; story revisions aligned to the updated personas and all checklist items above are checked.
- Deliverables: `../user-stories/stories.md` and `../user-stories/personas.md`.
- Current gate: stories and personas were explicitly approved on 2026-11-21; Application Design planning is underway.
