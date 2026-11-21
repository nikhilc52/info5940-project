# Document Requirements Questions

The two requested documents are available as draft templates. Answer these questions for assisted completion. Add a letter after each [Answer]: tag and provide details where requested.

## Question 1: Document scope

How should these documents be developed from the starter templates?

A) Keep them as templates for the team to fill in.

B) Populate them together; include a project summary, target users, problem, main capabilities, and success criteria after the answer.

X) Other (please describe after [Answer]: tag below).

[Answer]: A. We have filled in the required documents that you should read from.

## Question 2: Technical decisions

What technical environment should be recorded?

A) Leave technology choices undecided until the project vision is established.

B) Record an existing agreed stack; list languages, frameworks, data services, hosting, required versions, and any course constraints after the answer.

X) Other (please describe after [Answer]: tag below).

[Answer]: A. Technology choices can now be seen in the technical-environment.md document.

## Question 3: Security Extensions

Should security extension rules be enforced for this project?

A) Yes - enforce all SECURITY rules as blocking constraints (recommended for production-grade applications).

B) No - skip all SECURITY rules (suitable for PoCs, prototypes, and experimental projects).

X) Other (please describe after [Answer]: tag below).

[Answer]: Other. Security rules should be standard for prototypes, which is what our MVP product is.

## Question 4: Resiliency Extensions

Should the resiliency baseline be applied to this project?

This extension provides directional design-time guidance derived from the AWS Well-Architected Reliability Pillar, covering fault tolerance, availability, observability, and recovery. It is a first draft of resiliency decisions, not a production-readiness certification or an availability, RTO, or RPO guarantee; it does not replace a formal review of the built system.

A) Yes - apply the resiliency baseline as directional best practices and design-time guidance, to be validated before go-live.

B) No - skip the resiliency baseline (suitable for PoCs, prototypes, and experimental projects).

X) Other (please describe after [Answer]: tag below).

[Answer]: B. Skip the baseline for now, since this is a prototype model.

## Question 5: Property-Based Testing Extension

Should property-based testing (PBT) rules be enforced for this project?

A) Yes - enforce all PBT rules as blocking constraints (recommended for business logic, data transformations, serialization, or stateful components).

B) Partial - enforce PBT rules only for pure functions and serialization round-trips.

C) No - skip all PBT rules (suitable for simple CRUD applications, UI-only projects, or thin integration layers).

X) Other (please describe after [Answer]: tag below).

[Answer]: B. Partial. PBT rules should only be introduced for certain function tests, where non-PBT tests might be difficult to create. When in doubt as to which style of test to write, ask before writing.
