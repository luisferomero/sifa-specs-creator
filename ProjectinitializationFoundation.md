# Project Initialization Foundation

## 1. Project Purpose

### What are we building?

`sifa-specs-creator` is a web application that helps SIFA stakeholders document legacy screens and features without needing to write formal product requirements.

A stakeholder can:

- Select a Module.
- Create a Feature or Screen.
- Identify who uses it.
- Upload a screenshot of SIFA Desktop.
- Explain in plain Spanish what the screen does.
- Describe the normal workflow.
- Document known rules, restrictions, or special cases.

AI then analyzes the information, asks a short clarification interview when needed, and converts the approved knowledge into structured requirements.

The final output of the application is an approved Markdown (`.md`) requirements document that can later be manually used as input for GitHub Spec Kit.

---

## 2. Who Is It For?

### Primary User

Spanish-speaking SIFA stakeholders who:

- Know the business processes.
- Understand how the current system works.
- May not have Product Management experience.
- Need a simple way to explain requirements asynchronously.

### Secondary Users

- Product Lead
- Software Engineers
- Technical Leads
- Architects
- QA Engineers

---

## 3. Problem We Are Solving

A large amount of SIFA business knowledge exists in:

- The current desktop application.
- Existing workflows.
- Stakeholders' experience.
- Informal conversations.

Capturing this knowledge currently requires long discovery meetings and repeated clarification.

The goal of this application is to make discovery more asynchronous and reduce communication friction between business and technical teams.

Stakeholders should explain the business in their own words.

The application should handle the structure.

---

## 4. Core Workflow

```text
Stakeholder
    ↓
Create Feature
    ↓
Upload Screenshot
    ↓
Explain Purpose & Workflow
    ↓
AI Analysis
    ↓
Short AI Interview
    ↓
Review What AI Understood
    ↓
Generate Requirements
    ↓
Human Review / Approval
    ↓
Export Feature.md
```

After export, the engineering team may manually use the Markdown document fro creating specs documents.

---

## 5. Stakeholder Intake

The initial Feature intake should remain minimal.

The stakeholder provides:

- Module
- Feature / Screen Name
- Who uses this screen?
- Screenshot
- What is this screen used for?
- Step-by-step explanation of what the user normally does
- Known rules, restrictions, or special cases

The stakeholder should not need to understand formal requirement terminology.

---

## 6. AI Interview

AI should only ask questions that help resolve important missing information or ambiguity.

### Interview Rules

- Maximum 7 questions per Feature.
- Stop earlier when enough information exists.
- Ask one question at a time.
- Prefer closed questions.
- Maximum 4 options per question.
- Prefer multi-select when several answers may apply.
- Allow optional free-text context.
- Do not repeat information already provided.
- Questions must be specific to the current Feature.

The interview should primarily clarify:

1. Preconditions
2. Roles and permissions
3. Expected outcomes
4. Exceptions
5. Validations or errors
6. Dependencies with other SIFA areas
7. Current behavior vs desired behavior

These are areas to cover, not seven mandatory questions.

---

## 7. Requirements Output

The application generates structured:

- User Stories
- Business Rules
- Functional Requirements
- Acceptance Criteria
- Open Questions

These requirements remain proposals until reviewed and approved.

The approved requirements are then exported into a Markdown document.

Example:

```text
Feature
├── User Stories
├── Business Rules
├── Functional Requirements
├── Acceptance Criteria
├── Open Questions
└── Sources
```

The database remains the source of truth.

The `.md` file is an export of the approved information.

---

## 8. Human Approval

AI must never silently establish business behavior as fact.

Generated requirements should support states such as:

```text
Draft
↓
Needs Review
↓
Approved
```

A human must approve requirements before they are treated as authoritative.

When information is uncertain, the system should:

- Ask a clarification question.
- Create an Open Question.
- Mark the requirement for review.

It should not guess.

---

## 9. Traceability

Whenever possible, generated requirements should be traceable to the information that produced them.

Example:

```text
Stakeholder Input
      ↓
Interview Answer
      ↓
Business Rule
      ↓
Functional Requirement
      ↓
Acceptance Criteria
```

The system should make it possible to answer:

> Why does this requirement exist?

---

## 10. Language

The stakeholder experience is Spanish-first.

- Stakeholder input remains in Spanish.
- AI interviews are in Spanish.
- Requirements shown for review are in Spanish.
- Original stakeholder wording should be preserved.

Technical identifiers may remain language-independent.

Engineering artifacts may later be created in English if needed.

The stakeholder's approved Spanish requirements remain the original business source.

---

## 11. Current vs Desired Behavior

The application must distinguish between:

```text
Current SIFA Behavior
Desired Behavior
Both
Unknown / Needs Decision
```

Existing behavior should not automatically be assumed to be correct or desirable.

This is especially important during the migration from SIFA Desktop to the new system.

---

## 12. Domain Structure

The high-level project structure is:

```text
Project
└── Module
    └── Feature
        ├── Screens
        ├── Requirements
        ├── Sources
        └── Open Questions
```

Project-level concepts may also include:

```text
Actors
Glossary
```

The Feature is the main unit of discovery.

A Feature may involve one or more Screens.

---

## 13. AI Rules

AI may:

- Analyze stakeholder input.
- Analyze screenshots.
- Identify ambiguity.
- Ask clarification questions.
- Generate structured requirements.

AI must not invent:

- Business rules.
- Roles.
- Permissions.
- Workflows.
- Integrations.
- Exceptions.
- Legacy behavior.

AI-generated information must remain reviewable.

---

## 14. POC Boundaries

The POC intentionally does not include:

- Automatic Spec Kit execution.
- Automatic GitHub publishing.
- Screenshot annotations.
- Automatic UI element detection.
- Autonomous agents.
- RAG or vector databases.
- Fine-tuning.
- Superpowers integration.
- Automatic code generation from stakeholder input.

These capabilities may be considered later if the core workflow proves useful.

---

## 15. Success Criteria

The POC is successful if a Spanish-speaking stakeholder can document a SIFA Feature without understanding formal requirements engineering.

A stakeholder should be able to:

1. Create a Feature.
2. Upload a screenshot.
3. Explain the workflow in plain Spanish.
4. Complete a short AI-guided interview.
5. Review what the AI understood.
6. Approve the generated requirements.

The application should then produce a clean Markdown document containing enough approved business context to be used manually as input for GitHub Spec Kit.
