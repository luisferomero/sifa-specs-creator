<!--
Sync Impact Report (temporary review material; remove before committing)
- Version change: unversioned scaffold -> 1.0.0 (initial adoption)
- Modified principles: five scaffold slots replaced by:
  I. Spanish-First Stakeholder Discovery
  II. Evidence Before Inference
  III. Human Approval Before Authority
  IV. Traceable Requirements and Database Authority
  V. Focused POC Scope
- Added sections: Product and Information Boundaries; Delivery and Quality Gates;
  concrete Governance rules
- Removed sections: none
- Follow-up TODOs: none
- Dates: initial ratification and amendment recorded as 2026-09-18.
-->

# SIFA Specs Creator Constitution

## Core Principles

### I. Spanish-First Stakeholder Discovery

The web application MUST let SIFA stakeholders document legacy Features and Screens
asynchronously in plain Spanish without understanding formal requirements terminology. It MUST
provide the requirements structure. Stakeholder input, AI interviews, and requirements shown
for review MUST remain in Spanish, and original stakeholder wording MUST be preserved alongside
derived content. Technical identifiers MAY be language-independent; later engineering artifacts
MAY be in English, but approved Spanish requirements remain the original business source.

Rationale: the people who know SIFA's business processes must be able to contribute without
product-management expertise or repeated discovery meetings.

### II. Evidence Before Inference

AI MAY analyze stakeholder input and screenshots, identify ambiguity, ask clarification
questions, and propose structured requirements. It MUST NOT invent business rules, roles,
permissions, workflows, integrations, exceptions, or legacy behavior. Uncertainty MUST be
addressed through a clarification question, an Open Question, or an explicit review marker;
AI MUST NOT present an unsupported inference as established behavior. All generated content
MUST remain available for human review.

Rationale: acknowledging missing knowledge protects the migration from plausible but unsupported
business assumptions.

### III. Human Approval Before Authority

Generated requirements MUST remain proposals until explicitly reviewed and approved by a human.
The lifecycle MUST distinguish Draft, Needs Review, and Approved information. Stakeholders MUST
be able to review what AI understood before requirements generation and review the generated
requirements before approval. Only human-approved requirements may be treated as authoritative
or included as approved requirements in the final Markdown export. Any unresolved Open Questions
included in that export MUST remain clearly identified as unresolved.

Rationale: SIFA's business authority belongs to people; generation and export cannot substitute
for their approval.

### IV. Traceable Requirements and Database Authority

Generated requirements MUST reference their supporting stakeholder input, interview answers,
screenshots, or other captured sources whenever that support is available. Missing support MUST
be visible as uncertainty; the system MUST NOT fabricate a source. Relationships between
Business Rules, Functional Requirements, and Acceptance Criteria MUST be retained where one
produces or supports another, so reviewers can answer why a requirement exists.

The database MUST remain the source of truth. The Markdown document MUST be an export of the
approved database content and MUST include User Stories, Business Rules, Functional Requirements,
Acceptance Criteria, Open Questions, and Sources. Engineering teams MAY manually use the export
as input for GitHub Spec Kit.

Rationale: evidence and approval must remain inspectable even when requirements are handed off
outside the application.

### V. Focused POC Scope

The POC MUST validate the workflow from stakeholder discovery to approved Markdown. Its scope
MUST exclude automatic Spec Kit execution, automatic GitHub publishing, screenshot annotations,
automatic UI-element detection, autonomous agents, RAG or vector databases, fine-tuning,
Superpowers integration, and automatic code generation from stakeholder input. Adding these
capabilities requires a documented scope amendment after evaluation of the core workflow.

Rationale: a focused POC provides evidence that asynchronous discovery is useful before adding
automation or architectural complexity.

## Product and Information Boundaries

The domain MUST organize Projects into Modules and Modules into Features. The Feature MUST be
the main discovery unit, support one or more Screens, and group its Requirements, Sources, and
Open Questions. Actors and a Glossary MAY be defined at Project level. The initial stakeholder
intake MUST remain limited to Module, Feature or Screen name, who uses it, screenshot, purpose,
normal step-by-step workflow, and known rules, restrictions, or special cases.

The AI interview MUST target only material gaps specific to the current Feature and follow these
rules:

- Ask no more than seven questions per Feature, one at a time; skip or stop the interview when
  sufficient information exists. The seven-question limit is a ceiling, not a quota.
- Prefer closed questions when they can resolve the gap. Each question MUST offer no more than
  four answer options. Prefer multi-select when several answers may apply, to avoid forcing a
  single answer. Optional free-text context MUST be available.
- Do not repeat information already supplied. Relevant clarification areas are preconditions,
  roles and permissions, expected outcomes, exceptions, validations or errors, dependencies on
  other SIFA areas, and current versus desired behavior. These areas are not mandatory questions.
- Any material uncertainty remaining at the question limit MUST remain an Open Question or an
  explicit review marker rather than being resolved through a guess.

Documented behavior MUST distinguish Current SIFA Behavior, Desired Behavior, Both, and Unknown /
Needs Decision. Existing behavior MUST NOT automatically be classified as correct or desirable.
This distinction MUST remain visible through review and requirements export so that legacy
behavior is not silently adopted as a migration requirement.

## Delivery and Quality Gates

Specifications, implementation plans, and reviews MUST check the principles and POC boundaries
above. Changes affecting AI analysis, interviews, approval, or export MUST include verification
appropriate to the change: preservation of Spanish source wording, supported claims, interview
limits and early stopping, explicit uncertainty, behavior classification, human approval,
traceability, and export consistency with approved database content.

POC acceptance MUST demonstrate that a Spanish-speaking stakeholder without formal requirements
expertise can create a Feature, upload a screenshot, explain the purpose and normal
workflow, complete any necessary short interview, review what AI understood, review and approve
the generated requirements, and export a clean Markdown document. The exported document MUST
contain sufficient approved business context for manual use with GitHub Spec Kit and identify
remaining Open Questions. Local development MUST follow the Windows and PowerShell guidance
in `AGENTS.md`.

## Governance

This constitution governs product scope and development decisions for `sifa-specs-creator`.
Conflicting specifications, plans, or implementation decisions MUST be reconciled with it before
the affected work is accepted. Proposed amendments MUST document the rationale, affected
principles, compatibility or migration impact, and required updates to dependent artifacts;
the project maintainer MUST approve the amendment. Expanding an explicit POC exclusion requires
such an amendment, not an undocumented exception.

Constitution versions MUST follow semantic versioning: MAJOR for incompatible removal or
redefinition of governance, MINOR for a new principle or materially expanded guidance, and PATCH
for clarifications that do not change governance intent. Amendments MUST preserve the original
ratification date and update the last-amended date. Version 1.0.0 establishes the initial
governance baseline from the Project Initialization Foundation.

Every implementation plan, change review, and release decision MUST document applicable
compliance checks and resolve violations before acceptance. Any proposal requiring different
governance MUST identify that dependency explicitly and obtain an amendment before delivery.

**Version**: 1.0.0 | **Ratified**: 2026-09-18 | **Last Amended**: 2026-09-18
