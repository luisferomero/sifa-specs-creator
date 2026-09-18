# Implementation Plan: SIFA Feature Discovery and Approved Requirements Export

**Git branch**: `dev` | **Date**: 2026-09-18 | **Spec**: [spec.md](./spec.md)  
**Feature directory / SpecKit identifier**: `001-capture-feature-requirements`  
**Technical reference**: [ARCHITECTURE.md](../../ARCHITECTURE.md)

The setup script reports the feature-directory identifier as BRANCH when no SPECIFY_FEATURE override exists. The verified Git branch is dev; this planning run does not create or switch branches. The repository currently contains planning documents, not an implemented application.

## Summary

Deliver the complete Spanish self-service workflow: authenticate, select a Module, capture and resume a Feature with real screenshots, clarify material gaps, review understanding, generate and edit requirements, approve an exact reviewed revision, and download Markdown. All five user stories are acceptance scope.

Follow ARCHITECTURE.md: one Next.js application with React/Tailwind/shadcn UI; Supabase Auth, relational PostgreSQL, and private Storage; server-only OpenAI Responses calls; Vercel hosting. PostgreSQL owns sources, questions, requirements, human changes, approval, and state. The Markdown handoff is deterministic and ends the application workflow.

The constitution and clarified specification govern product behavior. Architecture suggestions are adapted accordingly: Project-level Actors/Glossary and shared Screens are deferred; application participants have equal rights; REJECTED is an item/proposal disposition, not a fourth Feature lifecycle state; interpreting screenshots does not implement automatic UI detection.

## Technical Context

**Language/Version**: TypeScript 5.x, Node.js 24.x, SQL migrations; Windows 11/PowerShell 7 development.  
**Primary Dependencies**: Next.js 16 App Router, React 19, Tailwind CSS 4, shadcn/ui, Supabase JS v2 and SSR helpers, Zod, OpenAI JS SDK, Sharp, tus-js-client. Pin compatible stable patches in the implementation lockfile.  
**Storage**: Supabase PostgreSQL for relational domain data and run metadata; private Supabase Storage for immutable screenshot bytes. JSONB only for provider/schema diagnostics and flexible AI metadata.  
**AI Baseline**: Responses with strict structured output; OPENAI_MODEL initially gpt-4.1-2025-04-14, subject to the documented evidence evaluation. The three operation boundaries match ARCHITECTURE.md.  
**Testing**: Vitest for domain/validation/export; PostgreSQL/RLS integration tests using a disposable Supabase stack; Playwright for Spanish browser journeys, two-session conflicts, and accessibility checks; controlled live AI evaluation.  
**Target Platform**: Modern desktop browsers on Windows; Vercel Node runtime, Supabase managed services; no offline requirement.  
**Project Type**: Single full-stack web application.  
**Performance Goals**: Spec targets: at least 4/5 users finish a familiar Feature within 20 minutes; two reviewers locate evidence for five requirements within 2 minutes each. Engineering targets, not measured promises: p95 non-AI metadata actions <=2 seconds at 10 concurrent participants; immediate Spanish pending feedback; bounded AI deadline 240 seconds within a 300-second route budget.  
**Constraints**: Spanish UI/output; preserve original sources; PNG/JPEG <=10,000,000 bytes; seven questions lifetime per Feature, four choices maximum, one at a time; source/current-set freshness; equal Project rights; real data isolation; explicit approval; recoverable soft deletion. Image bytes bypass Vercel body limits via private authenticated Storage.  
**Scale/Scope**: Planning load fixture: 10 concurrent participants and 1,000 Features, not a product cap. One pre-provisioned SIFA Project with agreed Modules for evaluation; second Project for isolation tests. No user/Project/Module administration UI, full history browser, purge, or image bundle export.

## Constitution Check

**Pre-research gate: PASS.** The supplied architecture supports the constitution; the adaptations above resolve its optional domain/status examples without an amendment.

| Gate | Design evidence | Required validation |
|---|---|---|
| I. Spanish-first discovery | Minimal intake, Spanish controls/errors, immutable original Sources, identified corrections | Spanish E2E, source-preservation assertions, SC-002/008 user sessions |
| II. Evidence before inference | Validated source manifest, explicit uncertainty/conflicts, visible-only screenshot interpretation | Invalid source/unsupported assertion fixtures; human evidence review; zero unsupported approved claims in evaluation |
| III. Human approval | Fresh understanding before generation; separate generation, acceptance, reconciliation, approval, export | Lifecycle/race tests; acknowledgement cannot approve an unsupported dependent assertion |
| IV. Traceability/database authority | First-class requirements, relational links, immutable set revisions, exact approval projection | Relationship integrity and deterministic Markdown comparison |
| V. Focused POC | One application/provider; manual engineering handoff | No agents, RAG, automatic code/GitHub/SpecKit, annotations, UI detection, or fine-tuning in product |
| Domain/interview boundaries | Project > Module > Feature > Screens; persistent question ordinals; explicit intent | Multi-Screen test, zero/seven question cases, reload/retry races, intent export |
| Quality and Windows workflow | Unit/integration/E2E plus five-user evaluation; npm scripts and PowerShell guide | [quickstart.md](./quickstart.md), including SC-001–009 |

No constitutional exception is needed. Research agents used for this engineering plan do not add autonomous agents to the product.

## Project Structure

### Documentation (this feature)

```text
specs/001-capture-feature-requirements/
├── spec.md                         # Existing input, preserved
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   ├── application.md
│   ├── ai-service.md
│   └── markdown-export.md
└── tasks.md                        # Future speckit-tasks output
```

### Proposed source code (repository root)

```text
src/
├── app/
│   ├── (auth)/                     # Spanish sign-in, recovery
│   ├── (workspace)/projects/[projectId]/features/[featureId]/
│   └── api/projects/[projectId]/features/[featureId]/
│       ├── ai/                     # Bounded analyze/question/generate routes
│       └── export/                 # Approved Markdown only
├── components/                     # Intake, Screens, interview, review, conflicts
├── domain/                         # Types, lifecycle, evidence and review rules
├── server/
│   ├── ai/
│   │   ├── prompts/
│   │   ├── schemas/
│   │   └── ai-service.ts
│   ├── features/                   # Commands, uploads, understanding, interview
│   ├── requirements/               # Revision, reconciliation, acceptance, approval
│   └── exports/                    # Approved projection and Markdown serializer
└── lib/
    ├── supabase/                   # Browser/server clients, generated DB types
    ├── validation/
    └── i18n/                       # Spanish messages and enum labels
supabase/
├── migrations/                     # Tables, constraints, RLS, transaction RPCs
├── seed.sql                        # Non-sensitive local fixtures only
└── tests/
tests/
├── unit/
├── integration/
├── e2e/
└── fixtures/                       # Synthetic images and deterministic AI responses
scripts/                            # Windows-compatible setup/validation wrappers
```

**Structure Decision**: Adopt the reference architecture's module layout. Domain command modules call narrow Supabase transactions directly; introduce no generic repository layer or separate Express/API deployment. All source paths above are proposed implementation paths.

## Phase 0: Research

Completed in [research.md](./research.md). Stack, identity, upload transport, concurrency, AI schema/model baseline, real-data handling, operation deadlines, interview counting, deletion, and export are resolved. Citations distinguish vendor facts from application design choices.

## Phase 1: Design

### Workflow and transaction boundaries

1. Save partial Drafts; validate analysis prerequisites only when analysis is requested. Unknown users/rules are explicit gaps; no added formal intake questionnaire.
2. Prepare/upload/finalize screenshots. Server decoding and transaction publication separate accepted evidence from pending uploads.
3. Run analysis, then an adaptive interview only for material unresolved gaps. Persist one published question before returning it; reload never consumes a new ordinal.
4. Review/correct/confirm understanding. Corrections create Sources and confirm the resulting source revision atomically.
5. First generation creates the current Needs Review set. Later generation creates a separate replacement; acceptance/rejection preserves current and historical human edits.
6. Review evidence, content, intent, and gaps. Explicitly reconcile changes since the generating source baseline. Unsupported assertions must be excluded or resolved; unanswered questions stay visible.
7. Approve an exact revision. A consistent authorization-checked projection yields Markdown without AI.
8. Soft deletion immediately hides the Feature and descendants while retaining accepted records/files. In-flight AI cannot publish into deleted content.

The [data model](./data-model.md) specifies relationships, revision tokens, counters, locking, and invalidation. The [application contract](./contracts/application.md), [AI contract](./contracts/ai-service.md), and [export contract](./contracts/markdown-export.md) define the interfaces.

### Access and failure handling

Active membership checks apply to all commands, domain-table RLS, and private Storage RLS. Reject cross-Project identifiers even for members of both Projects. Private responses cannot enter shared caches; actor identity is server-derived.

Use short transactions, expected review tokens, and idempotency receipts. Never hold locks across uploads or AI. On conflict preserve unsaved edits in component memory, show latest content alongside them, require explicit reconciliation, then submit with a new token. Never silently overwrite with a refreshed token.

Persist AI run leases and bounded deadlines. Failed, stale, incomplete, or unauthorized completion cannot replace requirements or consume a question. Show a Spanish notice before first AI analysis; operational logs exclude source bodies, screenshots, access tokens, and secrets.

### Verification and readiness

Implement [quickstart.md](./quickstart.md). High-risk checks cover Storage authorization, transaction races, lost responses, unsupported but schema-valid AI claims, and preservation of Spanish/human sources. Provisioning credentials, invited participants, Modules, provider access, duration/storage settings, and recoverable backups are setup dependencies, not unresolved product behavior. No live service was provisioned or verified by this planning run. Future retention/purge policy remains deferred per the spec; accepted records remain retained.

## Constitution Check (Post-Design)

**Post-design gate: PASS for the design.** Every initial gate has a contract and validation scenario. The supplied architecture and clarified requirements are preserved, including self-service real-data use, equal rights, conflicts, regeneration, and soft deletion. Implementation tests and user acceptance remain future work.

## Complexity Tracking

No constitution violations. Immutable Sources/set revisions, relational evidence links, transactional commands, upload intents, and AI leases are necessary for the stated concurrency, authority, recovery, and file-size requirements. Full event sourcing, queues, microservices, generic repositories, and a history-browsing UI are not required.
