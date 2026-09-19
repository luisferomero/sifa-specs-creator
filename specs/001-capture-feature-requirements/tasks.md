# Tasks: SIFA Feature Discovery and Approved Requirements Export

**Input**: [spec.md](./spec.md), [plan.md](./plan.md), [research.md](./research.md), [data-model.md](./data-model.md), [application contract](./contracts/application.md), [AI contract](./contracts/ai-service.md), and [export contract](./contracts/markdown-export.md).
**Reference**: [ARCHITECTURE.md](../../ARCHITECTURE.md), [constitution](../../.specify/memory/constitution.md), [quickstart.md](./quickstart.md).
**Created**: 2026-09-18
**Git branch**: `dev`; feature directory: `specs/001-capture-feature-requirements`.
**Scope**: All five user stories are required for POC acceptance. This document schedules implementation; no task is completed by generating it.

**Tests**: Included because the specification explicitly defines independent tests, acceptance scenarios and SC-001–009, and the constitution requires verification for AI/interview/approval/export changes. Within each story, author the listed tests first and observe meaningful expected failures before implementation; finish the story with passing checks.

**Existing reviewer gate**: [authority.md](./checklists/authority.md) contains 36 unchecked reviewer-owned requirements-quality items. Task generation does not evaluate or change them. Follow the implementation skill's checklist handling when implementation starts; these items are not application work-completion markers.

## Format and path conventions

- Each action uses `- [ ] T### [P?] [US#?] Description with file path`.
- Paths within task descriptions are repository-relative and refer to the planned single `src/` application, not separate frontend/backend services.
- `[P]` means independent files can be worked on in the stated wave **after every listed prerequisite is complete**. It does not remove dependencies or authorize overlapping edits to shared files.
- `[US1]`–`[US5]` map to the specification. Setup, foundation and final validation tasks have no story tag.
- Parenthesized dependencies are task IDs; prerequisite tests mean the test definitions have been written and shown to fail meaningfully, not that the missing implementation already passes.
- The data model's quoted enums, optional fields, byte limits, uniqueness rules and authority invariants are mandatory. All unspecified field definitions come from the linked data model; do not introduce extra stakeholder intake or product limits.
- Do not keep a database transaction open over a provider call or file transfer. Do not mark any pending proposal, failed operation, or mere source acknowledgement as authority.

## Phase 1: Setup (Shared Infrastructure)

**Goal**: Create the planned application skeleton and repeatable Windows-compatible development/test commands.

- [ ] T001 Scaffold the single Next.js 16 App Router application with React 19, TypeScript 5, Node.js 24.x, Tailwind 4/shadcn, Supabase JS/SSR, Zod, OpenAI SDK, Sharp and tus-js-client in package.json, package-lock.json, src/app/layout.tsx, and src/app/globals.css; preserve existing planning files and pin compatible stable patches.
- [ ] T002 [P] Configure strict TypeScript, explicit ESLint, and Node runtime settings in tsconfig.json, eslint.config.mjs, and next.config.ts; add the already planned lint/typecheck/build scripts to package.json in the later script-integration task. (depends on T001)
- [ ] T003 [P] Document public versus server-only variables and mock/live AI mode in .env.example and src/lib/validation/env.ts; protect .env.local and generated sensitive outputs in .gitignore; retain configurable OPENAI_MODEL with the plan's initial evaluation baseline. (depends on T001)
- [ ] T004 [P] Configure Vitest, Playwright, authenticated-session fixtures, database-test isolation and controlled mock-provider fixtures in vitest.config.ts, playwright.config.ts, tests/fixtures/session.ts, and tests/fixtures/ai-provider.ts; tests must never default to real SIFA data or live AI calls. (depends on T001)
- [ ] T005 [P] Configure disposable Supabase, disabled public signup, and PowerShell-compatible db:start/db:migrate/db:seed:test wrappers in supabase/config.toml and scripts/local-db.mjs; require an explicit non-local target for any managed-service operation and never silently reset an existing database. (depends on T001)
- [ ] T006 Wire dev/build/lint/typecheck/test/test:integration/test:e2e and db:* commands in package.json and document local startup in README.md; validate clean install, typecheck and empty test-harness discovery without claiming any story is implemented. (depends on T002, T003, T004, T005)

**Checkpoint**: Dependencies install reproducibly; configuration and isolated harness commands are available.

## Phase 2: Foundational (Blocking Prerequisites)

**Goal**: Establish containment, authenticated identity, live membership checks and atomic command semantics used by every story.

- [ ] T007 [P] Define command/result/error contracts, lifecycle/intent/requirement enums and ReviewToken in src/domain/contracts.ts; preserve "currentSetRevisionId (nullable), and understandingId (nullable)" and the exact contract error codes; derive actor identity exclusively from the authenticated session. (depends on T006)
- [ ] T008 [P] Create projects, project_memberships, modules and the Feature root in supabase/migrations/20260918000100_tenancy.sql; enforce "project_id + user_id primary key", "Module must share Project", states "DRAFT, NEEDS_REVIEW, APPROVED", "KNOWN/UNKNOWN/UNANSWERED" with rules adding "NONE_KNOWN", and nullable "current_set_revision_id?, current_understanding_id?, current_approval_id?, deleted_at?, deleted_by?"; use application/database UUIDs and database-generated UTC times. (depends on T006)
- [ ] T009 Add command_receipts, live membership/active-Feature helpers, restricted mutation grants, and the shared command boundary in supabase/migrations/20260918000200_command_boundary.sql; enforce "unique actor/command; reuse with different payload rejected", "membership, then Feature, then children" lock order, revision checks, rollback and post-revocation receipt protection; apply RLS to all existing domain tables and prohibit direct authority-state writes. (depends on T007, T008)
- [ ] T010 Build browser, verified SSR-session and isolated privileged Supabase clients in src/lib/supabase/browser.ts, src/lib/supabase/server.ts, src/lib/supabase/admin.ts, and src/proxy.ts; live membership stays database-authoritative and authenticated responses cannot enter shared caches. (depends on T003, T009)
- [ ] T011 Implement typed Server Action/route guards, same-origin/CSRF checks, ownership validation, idempotency receipt handling and Spanish-safe errors in src/server/features/command-context.ts and src/lib/validation/commands.ts; replay always rechecks authorization/deletion and reports result identities plus current permitted state. (depends on T010, T007)
- [ ] T012 [P] Create shared Spanish status/intent/error labels and keyboard-accessible pending/error primitives in src/lib/i18n/es.ts, src/components/shared/operation-feedback.tsx, and src/components/shared/form-field.tsx; use "Comportamiento actual de SIFA", "Comportamiento deseado", "Ambos", and "Desconocido / Requiere decisión". (depends on T011)
- [ ] T013 [P] Implement isolated fixture provisioning for A1/A2 in Project A, B1 in Project B and a dual-Project member in supabase/seed.sql and scripts/seed-test.mjs; include Modules and known test credentials without real screenshots or production membership changes. (depends on T011)
- [ ] T014 Verify live membership, ownership keys, direct-write denial, command replay with changed payload and after revocation, RLS, and rollback in supabase/tests/command-boundary.sql and tests/integration/authorization.test.ts; run the foundation checks using isolated fixtures and require them to pass before story work. (depends on T012, T013)

**Checkpoint**: T014 passes. All story work waits for the foundation; each later schema migration also includes RLS/ownership constraints for the tables it creates.

## Phase 3: User Story 1 — Capture and Resume Business Knowledge (Priority: P1)

**Goal**: Let an authorized stakeholder save/reopen partial Spanish intake, attach real screenshots, reconcile concurrent edits and soft-delete a Feature.

**Independent test**: Using provisioned Project/Module membership, A1 saves/reopens a partial Draft with two labeled Screens; A2 can edit it; invalid/private/stale requests preserve saved work; deletion removes ordinary access while retaining evidence. No AI is needed. US1.7 describes the whole POC and is finally accepted in T072/T081.

### Tests first

- [ ] T015 [P] [US1] Write failing capture/upload command contract tests in tests/contract/capture.test.ts for partial Drafts versus analysis prerequisites, equal Project rights, invalid inputs and conflict errors (FR-001–006, FR-021, FR-025). (depends on T014)
- [ ] T016 [P] [US1] Write failing integration tests in tests/integration/capture-storage.test.ts for source preservation, identical Screen labels, direct private Storage denial, lost-response finalization, conflicting saves and deletion; prepare fully decodable PNG/JPEG size-boundary fixtures in tests/fixtures/images.ts (V01–03, V08, V14–16). (depends on T014)
- [ ] T017 [P] [US1] Write failing two-session Spanish capture/resume scenarios in tests/e2e/capture.spec.ts covering invalid uploads, original wording, in-memory unsaved conflict reconciliation, retry conflicts, keyboard access and soft-delete confirmation (US1.1–6, US1.8–9). (depends on T014)
### Implementation

- [ ] T018 [US1] Create screens, sources, feature_source_links, upload_intents, attachments and source_assessments with containment FKs/RLS in supabase/migrations/20260918000300_capture.sql; preserve "detached_at?", "field_key?, original_text?, attachment_id?, screen_id?", "supersedes_source_id?", "inactive_from_revision?", "unique object_path", "upload_intent_id unique", "append-only", and "immutable object_path"; assessments preserve "ai_run_id? or human actor" and readability "AVAILABLE/LIMITED/UNREADABLE" (add the run FK when ai_runs exists); source kinds are "INTAKE, SCREENSHOT, INTERVIEW_ANSWER, HUMAN_CORRECTION, HUMAN_DECISION" and upload states "PREPARED -> UPLOADED (observed server-side) -> ACCEPTED, or REJECTED/EXPIRED". (depends on T015, T016, T017)
- [ ] T019 [US1] Implement transactional createFeature/saveIntake/saveScreen/detachScreen/detachAttachment/softDeleteFeature commands in supabase/migrations/20260918000400_capture_commands.sql; retain originals, apply source/edit revision rules, clear current approval after relevant changes, retain all accepted records/files, and make the tombstone immediately effective without any ordinary restore/purge action. (depends on T018)
- [ ] T020 [US1] Create the private screenshot bucket, intent preparation/publication RPCs and exact-object Storage policies in supabase/migrations/20260918000500_upload_commands.sql; enforce "bytes <=10,000,000, valid PNG/JPEG signature and MIME, and successful full decode" at publication, uploader-only pending INSERT, no client UPDATE/DELETE/upsert, no pending-source reads, and accepted-object reads requiring live membership plus active Feature. (depends on T019)
- [ ] T021 [P] [US1] Implement partial-save and separate analysis-readiness schemas in src/lib/validation/intake.ts and src/lib/validation/uploads.ts; preserve "Unknown is valid; unanswered is missing", require Module/name/accepted screenshot/nonblank purpose and workflow for analysis, and apply the inclusive 10,000,000-byte cap without inventing a product pixel limit. (depends on T020)
- [ ] T022 [P] [US1] Expose paginated listProjects/listModules/listFeatures/getFeature and capture/delete Server Actions in src/server/features/queries.ts and src/server/features/capture-actions.ts; use the common command boundary, stable source identities, active ancestors and source-preserving transactions. (depends on T020)
- [ ] T023 [US1] Implement prepareUpload/finalizeUpload in src/server/features/upload-actions.ts and src/server/features/image-validation.ts; fetch server-to-server, inspect actual bytes/MIME/signature, fully decode with Sharp, preserve originals/hash, then recheck membership/token before publication; failures retain existing content and conflicts leave the intent retryable. (depends on T021, T022)
- [ ] T024 [P] [US1] Implement Spanish invitation completion, sign-in, password recovery and sign-out in src/app/(auth)/sign-in/page.tsx, src/app/(auth)/reset-password/page.tsx, src/app/auth/callback/route.ts, and src/server/features/auth-actions.ts; keep Project/Module/member administration out of the stakeholder UI. (depends on T023)
- [ ] T025 [P] [US1] Build minimal Spanish intake, labeled Screens, explicit save state and unsaved-versus-latest conflict comparison in src/components/feature-intake/intake-form.tsx, src/components/feature-intake/screen-editor.tsx, and src/components/feature-intake/conflict-review.tsx; preserve local edits until explicit reconciliation and warn before discarding unsaved content. (depends on T023)
- [ ] T026 [P] [US1] Implement direct authenticated resumable TUS upload/progress/retry and accepted-image Blob previews in src/lib/supabase/screenshot-transfer.ts and src/components/feature-intake/screenshot-upload.tsx; reauthorize through Storage, release Blob URLs, avoid shared caching/public optimization/signed download links, and distinguish limited readability from invalid bytes. (depends on T023)
- [ ] T027 [US1] Compose authenticated Project/Feature lists and the capture/resume detail route in src/app/(workspace)/projects/page.tsx, src/app/(workspace)/projects/[projectId]/page.tsx, and src/app/(workspace)/projects/[projectId]/features/[featureId]/page.tsx; connect create/save/upload/delete and handle empty/error/deleted/revoked states in Spanish. (depends on T024, T025, T026)
- [ ] T028 [US1] Run and resolve US1 contract/integration/E2E failures in tests/contract/capture.test.ts, tests/integration/capture-storage.test.ts, and tests/e2e/capture.spec.ts; record only performed results in specs/001-capture-feature-requirements/validation/capture.md, keeping US1.7 full-workflow acceptance open until all stories integrate. (depends on T027)

**Checkpoint**: Capture/resume is a useful first milestone. It is not acceptance of the entire POC.

## Phase 4: User Story 2 — Review Understanding and Approve Requirements (Priority: P1)

**Goal**: Generate evidence-bound proposals from confirmed understanding and let any authorized Project participant correct, reconcile and explicitly approve the current set.

**Independent test**: Use an already captured Feature with sufficient information and controlled provider fixtures. A2 corrects/confirms A1's Feature, generates/reviews/approves it, and accepts or rejects regeneration without losing human edits. Gaps remain explicit. Freshness, unsupported-assertion and concurrency cases are isolated from the later interview UI.

### Tests first

- [ ] T029 [P] [US2] Write failing AI-analysis/generation and review-action contracts in tests/contract/review-ai.test.ts for confirmed understanding, strict source refs, initial versus replacement generation, rejection/acceptance and exact approval; fixtures include original/corrected Spanish content and unsupported but schema-valid claims (V06–11, V17). (depends on T028)
- [ ] T030 [P] [US2] Write failing correction-confirmation, source/current-set race, receipt freshness, equal-member approval and lost-response tests in tests/integration/review-approval.test.ts; require rejection of stale, revoked and deleted AI finalization and exclusion of unsupported/rejected dependencies. (depends on T028)
- [ ] T031 [P] [US2] Write failing Spanish complete-intake review/approval and replacement-comparison journeys in tests/e2e/review.spec.ts; A2 must review A1's Feature, preserve human edits on failure/rejection, acknowledge gaps separately and approve only the exact reconciled content. (depends on T028)
### Implementation

- [ ] T032 [US2] Create understanding_summaries/items/item_sources/gaps/gap_sources, ai_runs and ai_notice_receipts in supabase/migrations/20260918000600_understanding_ai.sql; add the pending source_assessments run FK and quote/enforce "created_by_run?, corrected_by?, confirmed_by?, confirmed_at?", "conflict_es?", "new content means new row", "same Feature", and AI status "RUNNING -> SUCCEEDED/FAILED/STALE/TIMED_OUT"; AI interpretations are never original Sources. (depends on T029, T030, T031)
- [ ] T033 [US2] Create shared interview_questions/options/answers/answer_choices for context reconstruction and later US4 behavior in supabase/migrations/20260918000700_interview_records.sql; enforce "ordinal 1..7", "Unique (feature_id, ordinal)", "only one OUTSTANDING question per Feature", "SINGLE/MULTIPLE/FREE_TEXT", "OUTSTANDING/ANSWERED/SKIPPED/RESOLVED_BY_SOURCE", "resolved_by_source_id?", "position 1..4", "unique question/position", "ANSWERED/UNKNOWN/SKIPPED", "context_es?", "supersedes_answer_id?", "append-only revisions", and "option must belong to answer's question" with same-Feature ownership/RLS. (depends on T032)
- [ ] T034 [US2] Create requirement_sets/set_revisions, requirements, requirement_sources/links, open_questions and their requirement/source/change links in supabase/migrations/20260918000800_requirement_revisions.sql; enforce "INITIAL/REGENERATION", "CURRENT/PROPOSED/REJECTED/SUPERSEDED", "base_current_set_revision_id?", "previous_revision_id?; immutable", "INCLUDED/REJECTED/UNCERTAIN", "DERIVES_FROM/SUPPORTED_BY; same revision, no self/cyclic derivation", "OPEN/RESOLVED", "resolution_source_id?", types "USER_STORY, BUSINESS_RULE, FUNCTIONAL_REQUIREMENT, ACCEPTANCE_CRITERIA", and intent "CURRENT, DESIRED, BOTH, UNKNOWN". (depends on T033)
- [ ] T035 [US2] Create review_receipts/items and relational source-link children plus approvals/approval_sources/approval_screens/approval_open_questions in supabase/migrations/20260918000900_review_approval.sql; enforce "support_review SUPPORTED/UNSUPPORTED", "immutable", frozen approver identity/context/Screen labels/evidence limitations, exact set/source/edit/understanding IDs, "all unresolved question IDs acknowledged", and "no INCLUDED item with missing/unsupported evidence or unresolved factual dependencies"; close all deferred current-pointer ownership FKs. (depends on T034)
- [ ] T036 [US2] Implement source-preserving confirmUnderstanding/reviseRequirements/resolveOpenQuestion/reconcileReview/approveRequirements/acceptReplacement/rejectReplacement transactions in supabase/migrations/20260918001000_review_commands.sql; confirm corrections against the resulting source revision, bind receipts to the current token, preserve historical edits, invalidate dependencies, keep replacement acceptance separate from approval, and reject stale proposal/source/current-set baselines. (depends on T035)
- [ ] T037 [US2] Implement AI notice/run-start/status/result-finalization transactions in supabase/migrations/20260918001100_ai_commands.sql; capture immutable context baselines, recheck actor/membership/deletion/lease before commit, prevent duplicate application, preserve current approval during proposal bookkeeping, and never hold database locks over provider calls. (depends on T036)
- [ ] T038 [P] [US2] Implement strict analysis/generation output schemas and source/link/coverage validation in src/server/ai/schemas/analysis.ts, src/server/ai/schemas/requirements.ts, and src/server/ai/validate-evidence.ts; use "SUPPORTED_CANDIDATE, UNCERTAIN, CONFLICT", reject fabricated/cross-Feature refs, incomplete output and hidden evidence omission, and keep absent categories explicitly empty. (depends on T037)
- [ ] T039 [P] [US2] Build immutable database-derived FeatureContext/RunContext in src/server/ai/context.ts with original input, accepted Screens/data URLs, current answers, prior questions, corrections and current human-edited set; include all source IDs sent, same-Feature checks and baseline versions, with bounded evidence batches or explicit failure rather than silent Screen omission. (depends on T037)
- [ ] T040 [P] [US2] Write versioned Spanish analyze/generate prompts in src/server/ai/prompts/analyze-feature.ts and src/server/ai/prompts/generate-requirements.ts; separate visible facts from unknown behavior, preserve conflicts/current-versus-desired intent, treat source text as untrusted evidence, and never request authority fields or automatic UI-element detection. (depends on T037)
- [ ] T041 [US2] Implement analyzeFeature/generateRequirements and their bounded runner in src/server/ai/ai-service.ts and src/server/ai/run-lifecycle.ts as the only production OpenAI caller; configure model/prompt/schema versions, strict Responses parsing, store:false/no tools/no conversation, a 240-second total deadline below the 300-second route budget, sanitized diagnostics and mock adapter; handle refusal, timeout, rate limits and retry without content loss. (depends on T038, T039, T040)
- [ ] T042 [US2] Expose understanding, requirement revision/gap resolution, reconciliation, replacement and approval Server Actions in src/server/features/understanding-actions.ts and src/server/requirements/review-actions.ts; require the exact reviewed token/receipt and acknowledged IDs and return truthful Spanish pending/failure states. (depends on T041)
- [ ] T043 [US2] Wire bounded analyze/generate Node routes in src/app/api/projects/[projectId]/features/[featureId]/ai/analyze/route.ts and src/app/api/projects/[projectId]/features/[featureId]/ai/generate/route.ts plus recordAiNotice/getAiRun in src/server/features/ai-actions.ts; preserve 200/201 success versus 202 existing-run status, current authorization, deadline and lost-connection recovery. (depends on T042)
- [ ] T044 [P] [US2] Build Spanish processing notice and understanding correction/confirmation UI in src/components/understanding/processing-notice.tsx and src/components/understanding/understanding-review.tsx; show original evidence, uncertainties and fresh summary status, record notice before first analysis, and never present confirmation as approval. (depends on T043)
- [ ] T045 [P] [US2] Build the six-category review editor with basic inspectable evidence links, behavior labels, dependency uncertainty, explicit empties and gap acknowledgement in src/components/requirements/requirements-review.tsx and src/components/requirements/approval-panel.tsx; allow any Project member to correct/reject and approve the complete reconciled set without regenerating supported human edits. (depends on T043)
- [ ] T046 [P] [US2] Build current-versus-proposed comparison in src/components/requirements/replacement-review.tsx; preserve current human edits, display stale proposal guidance, expose separate accept/reject controls and show accepted content in Needs Review pending separate approval. (depends on T043)
- [ ] T047 [US2] Integrate analysis, zero-question complete-input flow, understanding, review/replacement, run recovery and approval in src/components/features/feature-workflow.tsx and src/app/(workspace)/projects/[projectId]/features/[featureId]/page.tsx; unresolved-gap fixtures remain visible and do not masquerade as a completed interview before US4. (depends on T044, T045, T046)
- [ ] T048 [US2] Run and resolve US2 schema/contract/integration/E2E checks in tests/contract/review-ai.test.ts, tests/integration/review-approval.test.ts, and tests/e2e/review.spec.ts; record the complete-input independent result and retained unsupported gaps in specs/001-capture-feature-requirements/validation/review.md. (depends on T047)

**Checkpoint**: Complete-input analysis-to-approval works. Basic evidence links/support rules and intent labels are already required here; US5 later enriches their inspection. Shared interview records are introduced here because the AI context needs them; adaptive questions are delivered in US4.

## Phase 5: User Story 3 — Export an Approved Engineering Handoff (Priority: P1)

**Goal**: Download a faithful deterministic Spanish Markdown document for the exact current approval.

**Independent test**: Use a prepared Approved Feature containing all categories, intent values, source types and an acknowledged question. Repeated exports match that approved projection; stale/unapproved/revoked/deleted requests yield no document; editing a local download never changes the saved record.

### Tests first

- [ ] T049 [P] [US3] Write failing deterministic UTF-8 Markdown contract fixtures in tests/contract/markdown-export.test.ts and tests/fixtures/approved-export.ts with all types/intents, empty categories, original accents, screenshot references, rejected items and acknowledged uncertainties (V12, V19). (depends on T048)
- [ ] T050 [P] [US3] Write failing approval/edit/export snapshot race, stale approval ID, revocation and deletion tests in tests/integration/export-snapshot.test.ts and a download/retry/local-edit journey in tests/e2e/export.spec.ts; assert no mixed revisions and no AI call. (depends on T048)
### Implementation

- [ ] T051 [US3] Implement one consistent authorized projection transaction in supabase/migrations/20260918001200_export_projection.sql and src/server/exports/approved-projection.ts; require viewed approval/edit/source/set IDs, lock membership/Feature, freeze all approved fields, and distinguish changes before snapshot acquisition from changes after materialization. (depends on T049, T050)
- [ ] T052 [US3] Implement deterministic Spanish serialization in src/server/exports/markdown.ts and src/server/exports/filename.ts with Contexto and all six required sections, stored IDs/links/intent, frozen approval identity/time, explicit empty text, LF/UTF-8/escaping, no variable download timestamp and authenticated screenshot references explicitly saying images are not embedded. (depends on T051)
- [ ] T053 [US3] Implement GET export with approvalId/editRevision/sourceRevision/setRevisionId in src/app/api/projects/[projectId]/features/[featureId]/export/route.ts; use text/markdown; charset=utf-8, safe attachment filename and private no-store, returning a Spanish error without partial document or any content mutation. (depends on T052)
- [ ] T054 [US3] Add approved-only download and retry controls in src/components/requirements/export-button.tsx and integrate them in src/components/features/feature-workflow.tsx; send the approval actually viewed, reject stale state rather than silently selecting another approval, and explain the manual engineering handoff. (depends on T053)
- [ ] T055 [US3] Run and resolve US3 contract/race/browser tests in tests/contract/markdown-export.test.ts, tests/integration/export-snapshot.test.ts, and tests/e2e/export.spec.ts; record deterministic matches and denial cases in specs/001-capture-feature-requirements/validation/export.md. (depends on T054)

**Checkpoint**: The complete-input capture-to-approved-export increment works. It does not waive US4/US5 or the cross-cutting acceptance criteria.

## Phase 6: User Story 4 — Resolve Important Gaps in a Short Interview (Priority: P2)

**Goal**: Resolve material gaps with an adaptive Spanish interview that preserves answers and a maximum of seven published questions across sessions.

**Independent test**: Use complete, incomplete and conflicting prepared Features. Complete input needs zero questions; others receive at most one current question and four options, support context/unknown/skip, resume the same seventh question, and retain unresolved gaps without publishing an eighth question under races/retries.

### Tests first

- [ ] T056 [P] [US4] Write failing question/answer/stop contracts in tests/contract/interview.test.ts for complete-input zero questions, material-gap relevance, four options, single/multi-select, optional context, unknown/skipped responses and the still-outstanding seventh question (FR-010–013). (depends on T055)
- [ ] T057 [P] [US4] Write failing persistent-count/duplicate-request/lost-response/changed-evidence tests in tests/integration/interview-limit.test.ts and a resumed Spanish interview in tests/e2e/interview.spec.ts; source edits, retries and reopening must never reset the seven published slots (V04–05). (depends on T055)
### Implementation

- [ ] T058 [US4] Implement publish-question and answerQuestion transactions in supabase/migrations/20260918001300_interview_commands.sql; preserve "Multiple-choice questions have 1–4 options; free-text may have zero", "Blank/unknown/skipped answers supply no business fact", and "Count all retained published slots, including skipped/unanswered"; return a still-relevant outstanding slot first, retire a source-resolved gap without freeing its ordinal, and atomically enforce slot/option constraints from shared records. (depends on T056, T057)
- [ ] T059 [P] [US4] Implement ASK/STOP question output schema, relevance/known-gap checks and versioned Spanish prompt in src/server/ai/schemas/question.ts and src/server/ai/prompts/next-question.ts; preserve "SUFFICIENT or LIMIT or null", optionalContext:true, one specific material gap, <=4 choices and no mandatory seven-topic script. (depends on T058)
- [ ] T060 [P] [US4] Implement answer validation and Server Actions in src/lib/validation/interview.ts and src/server/features/interview-actions.ts; enforce question/option ownership, SINGLE/MULTIPLE cardinality, zero selections for UNKNOWN/SKIPPED, optional context, answer Source revisions and approval invalidation without resetting question history. (depends on T058)
- [ ] T061 [US4] Extend the only provider caller with getNextInterviewQuestion in src/server/ai/ai-service.ts and implement src/server/features/interview-service.ts; check existing slots/ceiling before provider work, account for persisted answers/new evidence, reject stale candidates, publish once, stop early, and carry remaining uncertainty forward at the limit. (depends on T059, T060)
- [ ] T062 [US4] Expose POST question in src/app/api/projects/[projectId]/features/[featureId]/ai/question/route.ts using the existing run lifecycle and authorization envelope; return a durable existing question or explicit stopped reason, and ensure an interrupted provider attempt consumes no slot. (depends on T061)
- [ ] T063 [US4] Build src/components/interview/interview-panel.tsx and integrate it in src/components/features/feature-workflow.tsx before understanding confirmation; provide Spanish single/multi-select, free context, unknown/skip, same-question resume, count/early-stop feedback and a refreshed summary after source-changing answers. (depends on T062)
- [ ] T064 [US4] Run and resolve US4 contract/concurrency/E2E cases in tests/contract/interview.test.ts, tests/integration/interview-limit.test.ts, and tests/e2e/interview.spec.ts; record zero/unnecessary-question and lifetime-limit results in specs/001-capture-feature-requirements/validation/interview.md. (depends on T063)

**Checkpoint**: The actual interview connects analysis to refreshed understanding; failed calls and source-resolved questions preserve lifetime counting.

## Phase 7: User Story 5 — Inspect Evidence and Distinguish Migration Intent (Priority: P2)

**Goal**: Make original evidence, human changes, conflicts, requirement derivation and current/desired behavior easy to inspect.

**Independent test**: Use prepared sources and requirements with text, screenshots, answers and corrections. A reviewer can identify supporting originals, distinguish all four intent values, see both sides of conflicts and inspect authenticated exported source links without treating uncertainty as approved behavior.

### Tests first

- [ ] T065 [P] [US5] Write failing provenance/intent/conflict contracts in tests/contract/evidence.test.ts using text, screenshot, answer and human-decision sources; assert no fabricated quotations, wrong-Screen attribution, unsupported dependency promotion or automatic desired-intent inference (US5.1–4). (depends on T064)
- [ ] T066 [P] [US5] Write failing reviewer journeys in tests/e2e/evidence.spec.ts for traceable rule-to-requirement-to-criterion relationships, current versus desired/unknown intent, conflicting sources, limited screenshots and authenticated source deep links shared by exports. (depends on T064)
### Implementation

- [ ] T067 [US5] Implement authorized provenance projection and lineage queries in src/server/requirements/evidence-queries.ts and src/domain/evidence.ts using existing relational Sources, change links and same-revision requirement links; return original text, source type/author, Screen identity, supporting/contradictory evidence and limited-readability markers without converting AI interpretations into Sources. (depends on T065, T066)
- [ ] T068 [P] [US5] Build inspectable source/Screen/answer/correction history in src/components/evidence/source-panel.tsx and derivation presentation in src/components/evidence/requirement-lineage.tsx; reuse private screenshot transfer, show original Spanish wording and evidence limitations, and retain distinct IDs for duplicate Screen labels. (depends on T067)
- [ ] T069 [P] [US5] Build explicit intent and conflicting-source decision UI in src/components/evidence/behavior-intent.tsx and src/components/evidence/conflict-decision.tsx; preserve "CURRENT, DESIRED, BOTH, UNKNOWN", capture real human decision Sources through review commands, and keep unresolved factual dependencies excluded from approved assertions. (depends on T067)
- [ ] T070 [US5] Integrate provenance panels and ?source={sourceId} links in src/components/requirements/requirements-review.tsx and src/app/(workspace)/projects/[projectId]/features/[featureId]/page.tsx; connect uncertainty decisions to new set revisions/reapproval and keep export terminology/source identifiers aligned. (depends on T068, T069)
- [ ] T071 [US5] Run and resolve US5 provenance/browser checks in tests/contract/evidence.test.ts and tests/e2e/evidence.spec.ts, rerun the affected approval/export checks, and record actual evidence/intent results in specs/001-capture-feature-requirements/validation/evidence.md. (depends on T070)

**Checkpoint**: All five story increments work together and are ready for the full self-service, real-data, performance and human evaluation gates.

## Phase 8: Polish & Cross-Cutting Validation

**Goal**: Integrate all stories, verify the planned operational boundaries, and obtain the measured POC acceptance evidence.

- [ ] T072 [P] Implement and run all-story self-service and post-approval mutation regression in tests/e2e/full-workflow.spec.ts and tests/integration/authority-boundaries.test.ts; cover US1.7, late-stage answer/Screen/gap changes, loss of support, real-data notice and no facilitator, then record V01–20 coverage in specs/001-capture-feature-requirements/validation/acceptance.md. (depends on T071)
- [ ] T073 [P] Audit and test keyboard navigation, focus, semantic labels, announced pending/errors and Spanish wording across authentication, intake, interview, comparison, evidence and export in tests/e2e/accessibility.spec.ts; record concrete findings in specs/001-capture-feature-requirements/validation/accessibility.md and resolve them before release. (depends on T071)
- [ ] T074 [P] Create the opt-in model evaluation runner and Spanish evidence scoring rubric in scripts/evaluate-ai.mjs and tests/fixtures/ai-evaluation.json; cover conflicting/incomplete/multi-Screen/injection/refusal fixtures, record model/prompt/schema versions and semantic-support findings in specs/001-capture-feature-requirements/validation/ai-evaluation.md, and require zero unsupported approved assertions before real-data release. (depends on T071)
- [ ] T075 [P] Write and exercise operator-only retained-record/backup recovery in docs/operations/recovery.md and scripts/recover-feature.mjs against an isolated copy; preserve screenshots and question counts, apply "clears current approval, advances edit_revision, and retains question history", keep restored sets NEEDS_REVIEW, and include no ordinary restore or permanent-purge capability (V16, SC-009). (depends on T071)
- [ ] T076 [P] Run cross-Project/revoked/deleted access and secret/log/cache checks in tests/integration/privacy-boundary.test.ts, including old JWTs, retained historical rows, Storage and retry receipts; document only sanitized findings in specs/001-capture-feature-requirements/validation/privacy.md (V14–18). (depends on T071)
- [ ] T077 [P] Measure planned metadata-action p95 <=2 seconds with 10 concurrent participants and 1,000 Features plus bounded AI failure feedback using scripts/measure-workflow.mjs; record measured versus target values and any required fixes in specs/001-capture-feature-requirements/validation/performance.md without treating fixture scale as a product cap. (depends on T071)
- [ ] T078 [P] Document invitation/member/Module provisioning, safe migrations, server-only environment variables, AI-data notice and actual retention settings, private Storage, separate test/live environments and 300-second route configuration in docs/operations/deployment.md; include >4.5 MB direct-transfer and live model-access smoke steps without claiming account-specific settings are enabled. (depends on T071)
- [ ] T079 Finish test:ai:live and performance command wiring in package.json, update README.md and specs/001-capture-feature-requirements/quickstart.md to match actual implemented commands, and add .github/workflows/ci.yml for lint/typecheck/unit/isolated integration/E2E/build without real-data fixtures or application-driven GitHub publishing. (depends on T072, T073, T074, T075, T076, T077, T078)
- [ ] T080 Against an explicitly selected authorized Vercel/Supabase environment, run the deployment smoke procedure and record sign-in, >4.5 MB direct image transfer, AI/review/approval/export, access revocation and recoverability in specs/001-capture-feature-requirements/validation/deployed-smoke.md; record unavailable credentials/environment as an unmet dependency, not a pass. (depends on T079)
- [ ] T081 Conduct the five-user self-service and two-engineer evaluation from quickstart.md, capturing 20-minute completion, two-minute source/intent/approval lookup, >=4/5 satisfaction and zero unsupported approved facts in specs/001-capture-feature-requirements/validation/user-evaluation.md; participants operate and approve their own work, and real evaluation data stays in authorized storage. (depends on T080)
- [ ] T082 Run final lint/typecheck/unit/integration/E2E/build and reconcile every FR-001–029 and SC-001–009 result in specs/001-capture-feature-requirements/validation/release.md; resolve failures and document external blockers truthfully, preserve reviewer-owned checklist markers, and declare POC completion only after all five stories and release criteria pass. (depends on T081)

**Checkpoint**: Only actual passing results close implementation tasks. Credential/environment/user availability is a release dependency to record honestly, never a reason to fabricate test completion.

## Dependencies & Execution Order

### Story graph

The conservative execution order preserves the specification's priorities and makes every prerequisite explicit:

```text
Setup T001–T006
  -> Foundation T007–T014
  -> US1 T015–T028: capture/resume
  -> US2 T029–T048: understanding/review/approval
  -> US3 T049–T055: approved Markdown
  -> US4 T056–T064: adaptive clarification
  -> US5 T065–T071: rich evidence/intent inspection
  -> Cross-cutting T072–T082: complete POC acceptance
```

US3 logically requires approved records/services from US2; it does not require live interview interaction and can use prepared records. US4 consumes US1 sources and US2's AI/understanding/shared interview records. US5 consumes the existing source, requirement, answer and approval structures. The ordering of US3 before US4 and US4 before US5 is the selected delivery sequence, not a claim that evidence rules or authorization may be deferred.

US2 supplies the minimum source/intent/dependency checks needed for safe approval. US5 adds richer inspection rather than retroactively securing earlier approvals. US1.7 and FR-027 span all story phases and cannot be accepted at the first milestone.

### Shared schema and contract ownership

| Responsibility | Earliest owner/tasks | Consumers |
|---|---|---|
| Project/Module/membership/Feature roots; revision tokens; command receipts | Foundation T007, T008, T009, T011 | All stories |
| Screens, immutable Sources, source history, upload intents/attachments, readability assessments | US1 T018, T019, T020 | US2–US5 |
| Understanding rows, AI runs/notice, shared interview records | US2 T032, T033 | US2, US4, US5 |
| Requirement sets/revisions, evidence links, Open Questions, change provenance | US2 T034, T036 | US2–US5 |
| Reconciliation receipts/items, exact approval and relational context/source/Screen snapshots | US2 T035, T036 | US2, US3, US5 |
| Export projection and deterministic output contract | US3 T051, T052, T053 | US3, cross-cutting |
| Question publication/counting, answers, source-resolved gaps | US4 T058, T061 | US4, US5 |
| Rich evidence projection and authenticated source deep link | US5 T067, T070 | US5, exported references |

Every Feature-owned table retains project_id/feature_id and ownership constraints, even when not repeated in a task. Add forward-reference FKs as soon as their targets exist: source_assessments -> ai_runs in T032 and Feature current pointers in T035. Before those schema steps the absent-domain pointers remain null and user commands cannot populate them.

New migrations follow the numbered dependency order. Regenerate Supabase database TypeScript types in src/lib/supabase/database.types.ts after each applied schema migration as part of that migration task; do not have parallel workers edit the generated types concurrently.

### Within a story

Write tests -> create constrained records -> implement transactional commands/services -> expose actions/routes -> build independent UI components -> integrate shared pages -> run story acceptance. Keep core operations in their owning server modules; the only production OpenAI caller remains ai-service.ts.

Shared-file changes to package.json and feature-workflow.tsx are deliberately assigned sequential integration tasks. If parallel validation finds source fixes, serialize those fixes in the owning module and rerun affected checks before closing the phase.

## Parallel Execution Examples

These examples describe disjoint work after prerequisites, not additional tasks or a requirement to spawn agents.

| Wave | Completed prerequisite | Tasks that may proceed together | Join |
|---|---|---|---|
| Setup | T001 | T002, T003, T004, T005 | T006 |
| Foundation types/schema | T006 | T007, T008 | T009 |
| Foundation labels/fixtures | T011 | T012, T013 | T014 |
| US1 contract/integration/browser tests | T014 | T015, T016, T017 | T018 |
| US1 validation/query modules | T020 | T021, T022 | T023 |
| US1 authentication/forms/transfer UI | T023 | T024, T025, T026 | T027 |
| US2 tests | T028 | T029, T030, T031 | T032 |
| US2 AI schema/context/prompts | T037 | T038, T039, T040 | T041 |
| US2 understanding/review/replacement components | T043 | T044, T045, T046 | T047 |
| US3 document and race/browser tests | T048 | T049, T050 | T051 |
| US4 tests | T055 | T056, T057 | T058 |
| US4 question schema and answer actions | T058 | T059, T060 | T061 |
| US5 tests | T064 | T065, T066 | T067 |
| US5 lineage and intent components | T067 | T068, T069 | T070 |
| Cross-cutting independent evidence | T071 | T072, T073, T074, T075, T076, T077, T078 | T079 |

## Requirement and Validation Traceability

| Requirement(s) | Implementation / primary verification tasks | Quickstart scenarios |
|---|---|---|
| FR-001, FR-002, FR-003, FR-005 | T008, T015–T019, T021–T022, T025, T027–T028 | V01, V08 |
| FR-004 | T016, T018, T020–T023, T026, T028, T080 | V02–03 |
| FR-006 | T012, T024–T028, T044–T047, T052, T063, T068–T073 | V01, V20 |
| FR-007, FR-008, FR-009 | T029, T032, T034–T041, T045, T065–T071, T074 | V06–07, V17 |
| FR-010, FR-011, FR-012, FR-013 | T033, T056–T064 | V04–05 |
| FR-014 | T029–T032, T036–T044, T047–T048 | V06 |
| FR-015, FR-016, FR-018 | T029–T030, T034–T048 | V09–10 |
| FR-017 | T034, T036, T039, T045, T052, T065–T071 | V07, V19 |
| FR-019 | T030–T031, T035–T036, T042, T045, T048, T050–T051 | V08, V11, V13 |
| FR-020 | T019, T023, T030, T036–T037, T050–T051, T058, T060, T072 | V10–13 |
| FR-021 | T008–T011, T014, T020, T026, T030, T037, T051, T076 | V14–15 |
| FR-022, FR-023, FR-024 | T035, T049–T055, T070 | V11–13, V19 |
| FR-025 | T009, T011–T012, T016–T017, T023, T025, T030, T037, T041, T043, T050, T057–T064 | V03, V05, V08–10, V13, V17 |
| FR-026 | T041, T052–T054, T078–T082 | V19 |
| FR-027 | All stories; T072–T073, T080–T082 | V01–20, especially US1.7 |
| FR-028 | T003, T012, T032, T041, T043–T044, T074, T076, T078, T080–T082 | V18, real-data evaluation |
| FR-029 | T019–T020, T022, T027–T028, T030, T037, T050–T051, T075–T076 | V16 |
| SC-001, SC-003, SC-005, SC-006, SC-009 | T028, T048, T055, T064, T071–T072, T075–T076, T080–T082 | All automated/operational acceptance |
| SC-002, SC-007, SC-008 | T077, T081–T082 | Five users and two reviewers |
| SC-004 | T045, T048, T065–T071, T074, T081–T082 | Semantic evidence review; schema validity alone is insufficient |

Coverage entries schedule the work; they are not passing results. Validation records are created only as their tasks run, contain no secrets or raw real-data fixtures, and distinguish synthetic automated checks from actual user/model evaluation.

## Implementation Strategy

1. Complete setup and foundation, then deliver US1 as the **first usable milestone**: save/resume Spanish business knowledge and screenshots safely without AI.
2. Add US2 and US3 for a complete-input approval/export increment using the same real authorization and evidence boundaries. Use prepared gap fixtures for isolated tests; do not claim adaptive clarification is complete yet.
3. Add US4's adaptive interview and US5's richer evidence/intent inspection. Run every story's independent checks and preserve earlier passing behavior.
4. Run cross-cutting checks, the configured-environment smoke test and the specified human evaluation. **The full POC includes all five stories**, even though US4/US5 have delivery priority P2.
5. Where credentials, a configured environment or representative participants are unavailable, finish the locally executable work and record the unmet external dependency. Do not mark the corresponding live or human validation task complete.
6. Before declaring the POC complete, close every implementation/acceptance task based on actual evidence and follow reviewer-owned checklist gating without altering review markers on the reviewer's behalf.

## Counts and Notes

- Total: 82 unchecked tasks.
- Setup: 6; foundation: 8; US1: 14; US2: 20; US3: 7; US4: 9; US5: 7; cross-cutting: 11.
- Parallel opportunities: 42 tasks in 15 explicitly defined waves.
- No automatic Spec Kit/GitHub publishing, separate Express service, autonomous agent, RAG/vector database, screenshot annotation, automatic UI detection, fine-tuning or code generation is included in the product.
- Project/Module/participant provisioning is an operator setup dependency; the stakeholder workflow after provisioning is self-service.
- Permanent deletion/retention-policy design and a public recovery UI stay outside this scope. Retain accepted evidence and validate recoverability as specified.
- This file is the sole task-generation output; implementation source paths above do not exist merely because they are listed.
