# Architecture

## 1. Goal

`sifa-specs-creator` is a lightweight web application that helps Spanish-speaking SIFA stakeholders describe legacy screens and features, answer a short AI-guided interview, and produce approved structured requirements.

The final output of the application is a Markdown (`.md`) requirements document.

GitHub Spec Kit is intentionally outside the application workflow for the POC.

---

## 2. Tech Stack

- **Framework:** Next.js
- **Language:** TypeScript
- **UI:** React + Tailwind CSS + shadcn/ui
- **Database:** Supabase PostgreSQL
- **Authentication:** Supabase Auth
- **File Storage:** Supabase Storage
- **AI:** OpenAI Responses API
- **Hosting:** Vercel
- **Source Control:** GitHub

A separate Express backend is not required.

Next.js handles both the frontend and server-side application logic.

---

## 3. High-Level Architecture

```text
Browser
   ↓
Next.js
   ├── UI
   ├── Server Actions / API Routes
   ├── Application Logic
   └── AI Service
          ↓
       OpenAI

Next.js
   ↓
Supabase
   ├── PostgreSQL
   ├── Auth
   └── Storage
```

All AI calls and sensitive operations must execute server-side.

---

## 4. Core Domain Model

```text
Project
└── Module
    └── Feature
        ├── Screens
        ├── Requirements
        ├── Sources
        └── Open Questions
```

Additional project-level entities:

```text
Actors
Glossary
```

### Requirement Types

The MVP supports:

```text
USER_STORY
BUSINESS_RULE
FUNCTIONAL_REQUIREMENT
ACCEPTANCE_CRITERIA
```

Requirements are first-class database entities.

They should not exist only as embedded JSON inside a Feature.

---

## 5. Database

PostgreSQL is the source of truth.

Initial tables may include:

```text
projects
modules
features

screens
feature_screens

actors
feature_actors

requirements
requirement_links

sources
requirement_sources

interview_questions
interview_answers

open_questions
attachments

ai_runs
```

Use relational tables for core business data.

Use `JSONB` only for flexible data such as:

- AI analysis metadata
- Raw structured AI responses
- Generation metadata
- Diagnostics

---

## 6. Screenshot Storage

SIFA Desktop screenshots are stored in Supabase Storage.

PostgreSQL stores their metadata and storage references.

Screenshots are supporting sources for understanding a Feature.

---

## 7. AI Architecture

AI is an internal backend service.

The MVP has three main AI operations:

```ts
analyzeFeature()

getNextInterviewQuestion()

generateRequirements()
```

The rest of the application should not call OpenAI directly.

---

## 8. AI Flow

### Feature Analysis

Input:

```text
Module
Feature / Screen Name
Actors
Screenshot
Purpose
Workflow
Known Rules / Special Cases
```

Output:

```text
Summary
Detected Actions
Business Facts
States
Possible Dependencies
Ambiguities
```

The result must be structured and validated before being stored.

---

### AI Interview

The interview is adaptive.

Rules:

- Maximum 7 questions per Feature
- One question at a time
- Prefer closed questions
- Maximum 4 options
- Prefer multi-select when multiple answers can apply
- Optional free-text context
- Do not repeat known information
- Stop early when enough information is available

The database stores all questions and answers.

AI conversation memory is not the source of truth.

---

### Requirements Generation

After the interview and understanding review, AI generates:

```text
User Stories
Business Rules
Functional Requirements
Acceptance Criteria
Open Questions
```

Generated requirements remain reviewable until approved.

---

## 9. Human Review

AI-generated requirements are proposals.

The application should support states such as:

```text
DRAFT
NEEDS_REVIEW
APPROVED
REJECTED
```

Only approved requirements should be considered authoritative.

AI must not approve requirements on behalf of the stakeholder or Product Lead.

---

## 10. AI Output Rules

Programmatic AI responses must use structured output.

```text
OpenAI
   ↓
Structured JSON
   ↓
Schema Validation
   ↓
Application Mapping
   ↓
PostgreSQL
```

AI must not control:

- Database IDs
- Approval states
- Authorization
- Timestamps
- Referential integrity

Those remain application responsibilities.

---

## 11. Traceability

Whenever possible, requirements should reference the information that produced them.

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

The system should make it possible to understand why a requirement exists.

---

## 12. Language

The stakeholder experience is Spanish-first.

Original stakeholder input remains in Spanish.

Requirements shown for stakeholder review should also be generated in Spanish.

Internal values remain language-independent.

Example:

```text
APPROVED
BUSINESS_RULE
PURCHASING_SUPERVISOR
CLOSED
```

The UI may display:

```text
Aprobado
Regla de Negocio
Supervisor de Compras
Cerrada
```

---

## 13. Markdown Export

The final output of an approved Feature is a Markdown requirements document.

The export should be deterministic and generated from approved database data.

It should not require another AI call.

Example flow:

```text
Approved Requirements
        ↓
Markdown Template
        ↓
feature-name.md
```

A generated document may contain:

```text
# Feature Name

## Context

## Actors

## User Stories

## Business Rules

## Functional Requirements

## Acceptance Criteria

## Open Questions

## Sources
```

The Markdown file is an export of the approved structured data.

It is not the primary source of truth.

---

## 14. Spec Kit Boundary

The application does not execute Spec Kit automatically.

The POC workflow ends after Markdown export.

```text
SIFA Specs Creator
        ↓
Approved Requirements
        ↓
Export Feature.md
────────────────────────
Manual Engineering Step
────────────────────────
        ↓
GitHub Spec Kit
        ↓
/speckit.specify
        ↓
spec.md
        ↓
/speckit.plan
        ↓
/speckit.tasks
        ↓
Implementation
```

The engineer manually chooses which exported Features are passed into Spec Kit.

This separation is intentional for the POC.

---

## 15. Security

Sensitive operations must run server-side.

Never expose:

```text
OPENAI_API_KEY
Supabase service credentials
```

Authorization must also be enforced server-side.

Supabase Row Level Security may be used where appropriate.

---

## 16. Suggested Project Structure

```text
src/
├── app/
├── components/
│
├── server/
│   ├── ai/
│   │   ├── prompts/
│   │   ├── schemas/
│   │   └── ai-service.ts
│   │
│   ├── features/
│   ├── requirements/
│   └── exports/
│
├── domain/
│
└── lib/
    ├── supabase/
    ├── validation/
    └── i18n/
```

This structure is guidance rather than a strict requirement.

Do not introduce abstractions before they are needed.

---

## 17. POC Boundaries

The first version intentionally excludes:

- Automatic Spec Kit execution
- Automatic GitHub publishing
- Microservices
- Express backend
- MongoDB / Firebase
- RAG
- Embeddings
- Vector databases
- LangChain / LangGraph
- Autonomous agents
- Fine-tuning
- Multiple AI providers
- Screenshot annotations
- Automatic UI detection
- Superpowers

These may be evaluated later if a real requirement justifies them.

---

## 18. Architecture Summary

```text
Application
→ Next.js + TypeScript

UI
→ React + Tailwind + shadcn/ui

Database
→ Supabase PostgreSQL

Auth
→ Supabase Auth

Storage
→ Supabase Storage

AI
→ OpenAI Responses API

Hosting
→ Vercel

Business Source Language
→ Spanish

Application State
→ PostgreSQL

AI State
→ Reconstructed from PostgreSQL

AI Output
→ Structured + validated

Requirements Authority
→ Human approval

Final Application Output
→ Markdown .md

Spec Kit Integration
→ Manual
```

The architecture should evolve only when a real requirement justifies additional complexity.
