# AI-DLC State Tracking

## Project Information

- Project: info5940-project
- Project type: Greenfield
- Current stage: INCEPTION complete; Construction is next and has not started
- Scope: Develop a locally run Course AIdvisor as defined in the approved requirements and execution plan.
- Rule details resolved to: aidlc-rules/aws-aidlc-rule-details/ (standard paths absent).

## Workspace State

- Existing application code: No
- Programming languages and build system: Not established
- Reverse engineering needed: No
- Workspace root: C:/Users/shaur/OneDrive/Desktop/Coding stuff/5940/info5940-project

## Code Location Rules

- Application code belongs at the workspace root, never in aidlc-docs/.
- Documentation belongs in aidlc-docs/.

## Stage Progress

- [x] Workspace Detection
- [x] Reverse Engineering assessed: skipped, no application code
- [x] Requirements Analysis: answers recorded, requirements revised, and explicitly approved
- [x] Workflow Planning: execution plan explicitly approved; User Stories, Application Design, Units Generation, and Construction are authorized
- [x] User Stories: generated personas and stories, including testable Economics major-change and ORIE graduation-requirement outcomes, explicitly approved by the user on 2026-11-21
- [x] Application Design: plan and generated artifacts explicitly approved; includes structured catalog requirements, lightweight review on every re-scrape, and derived CU Reviews sentiment scores
- [x] Units Generation: plan approved; three unit artifacts generated, validated, and explicitly approved by the user on 2026-11-21
- Construction and Operations: not started.

## Document Setup Plan

- [x] Locate and read common AI-DLC rules and extension opt-in prompts.
- [x] Inspect repository and identify existing documentation and code.
- [x] Validate plain Markdown content, table structure, and relative links before creation.
- [x] Create Vision draft template without inventing project requirements.
- [x] Create Technical Environment draft template with observed facts and pending decisions.
- [x] Create required requirements questions and record the workflow state.
- [x] Receive and validate team answers; repair the malformed Other option formatting while preserving answers.
- [x] Synthesize requirements from the completed Vision and Technical Environment; document measurable criteria, constraints, open decisions, and extension applicability.
- [x] Receive explicit approval of requirements before marking the stage complete or advancing to Workflow Planning.

## Extension Configuration

| Extension | Enabled | Decided At |
|---|---|---|
| Security Baseline | Limited: standard prototype practices | Requirements Analysis answer 3 |
| Resiliency Baseline | No | Requirements Analysis answer 4 |
| Property-Based Testing | Partial: selected function tests; ask when test style is uncertain | Requirements Analysis answer 5 |

## Extension Applicability for This Document Setup

- Security Baseline: enabled at limited prototype-standard scope per user response. Full baseline loaded; apply relevant standard controls during later design and implementation, with applicability assessed by stage.
- Resiliency Baseline: disabled by user response; full rules not loaded.
- Property-Based Testing: partial enforcement enabled per user response. Full rules loaded; only PBT-02, PBT-03, PBT-07, PBT-08, and PBT-09 are blocking. Use PBT selectively for functions where example-based tests are difficult; ask the user when the test style is uncertain.
- Full rules loaded for the limited Security Baseline and partial Property-Based Testing selections; full Resiliency Baseline rules remain unloaded.

## Next Step

Requirements in inception/requirements/requirements.md, the Workflow Planning execution plan at inception/plans/execution-plan.md, the story-generation plan and its deliverables, the Application Design plan and artifacts, and all three Units Generation artifacts were explicitly approved on 2026-11-21. INCEPTION is complete. The next AI-DLC step is CONSTRUCTION, beginning with Functional Design for Unit 1. Construction has not started; the user previously instructed to stop before it.

## Workflow Planning Summary

- **Stages recommended to execute**: User Stories, Workflow Planning, Application Design, Units Generation, Functional Design, NFR Requirements, NFR Design, Code Generation, Build and Test.
- **Stages assessed to skip**: Reverse Engineering (greenfield, already assessed), Infrastructure Design (local prototype), Operations (placeholder).
- **Current gate**: INCEPTION is complete. The next AI-DLC stage is CONSTRUCTION, beginning with Functional Design for Unit 1. Construction has not started; the user explicitly instructed to stop before Construction.
