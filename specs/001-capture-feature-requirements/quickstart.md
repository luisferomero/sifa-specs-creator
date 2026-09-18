# Quickstart: Implementation Validation Guide

**Status**: Planned validation workflow. This repository has no application package, migrations, npm scripts, or runnable POC yet. The commands below are the implementation contract for the next phase; they were not executed during planning.

## Prerequisites

Use Windows 11, PowerShell 7, Node.js 24.x, and npm. Install a Docker-compatible local runtime for the disposable Supabase test stack. Production-like validation uses a separate configured Supabase project and Vercel environment.

Prepare three identities: A1 and A2 are active members of Project A; B1 belongs only to Project B. Also test a dual-Project member to prove foreign IDs cannot be cross-linked. Seed agreed Modules and Spanish synthetic fixtures in the disposable test stack. Authorized end-user evaluation separately uses real SIFA screenshots and business details; anonymization is not a product prerequisite.

Implementation must provide .env.example documenting NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY, server-only SUPABASE_SECRET_KEY, OPENAI_API_KEY, OPENAI_MODEL, and APP_URL. Local legacy anon/service_role key equivalents may be mapped by the setup wrapper. Keep .env.local untracked and never prefix private keys with NEXT_PUBLIC_. Set OPENAI_MODEL to the evaluated snapshot recorded in research; configure local tests with the mocked AI adapter.

## Expected setup and command interface

After the implementation and lockfile exist, use these PowerShell-compatible commands from the repository root:

```powershell
npm ci
Copy-Item -LiteralPath '.env.example' -Destination '.env.local'
npm run db:start
npm run db:migrate
npm run db:seed:test
npm run dev
```

Copy the template only if .env.local does not exist; preserve existing credentials. The db:* scripts must use the project-pinned Supabase CLI/cross-platform wrappers and target the disposable local database by default. Provision Auth identities and membership through seed tooling, including invitation/sign-in/reset support for the real environment. The stakeholder needs only a sign-in and assigned Project after provisioning.

| Script | Required behavior |
|---|---|
| npm run lint | Explicit ESLint invocation appropriate to Next.js 16 |
| npm run typecheck | TypeScript no-emit validation |
| npm run test | Vitest domain, lifecycle, validation, serializer and mock-AI contracts |
| npm run test:integration | PostgreSQL transaction/RLS/Storage checks against isolated Supabase, including direct SDK/RPC attempts |
| npm run test:e2e | Playwright authenticated Spanish browser journeys using mock AI |
| npm run test:ai:live | Explicit opt-in live model evaluation; records model/prompt/schema versions and reviewed evidence findings |
| npm run build | Production application build and server/client secret-boundary checks |
| npm run db:start / db:migrate / db:seed:test | Start local stack, apply migrations, and seed isolated fixtures; never silently reset a production project |

Environment configuration must disable public signup, enable RLS and a private screenshot bucket, use the same 10,000,000-byte cap everywhere, and configure the documented route duration/deadline. A deployed smoke test is needed because local development does not reproduce Vercel payload limits.

## Acceptance scenarios

| ID | Steps | Expected result / coverage |
|---|---|---|
| V01 | As A1, save a partial Spanish Draft, close/reopen, then attempt analysis with missing fields and explicit unknown users/rules | Saved wording survives; missing fields get Spanish errors; explicit unknown is a gap, not invented knowledge. FR-001–006 |
| V02 | Add two distinct Screens with identical labels and valid PNG/JPEG files; upload exactly 10,000,000 bytes, above limit, corrupt and unsupported fixtures | Valid boundary file accepted when fully decodable; others rejected appropriately without losing saved sources; Screens remain distinct. A blurry valid file is limited evidence. FR-004, EC-02/08 |
| V03 | Upload a valid >4.5 MB file in the deployed environment using direct Storage/TUS; simulate interrupted upload and finalize conflict | No Next.js payload error; pending content is not a shared Source; retry finalizes once, immutable bytes preserved. FR-004/025 |
| V04 | Complete-input fixture then incomplete/conflicting fixtures | Complete input asks zero unnecessary questions; others ask material gap questions one at a time with <=4 options and optional text; unknown/skip invents no answer. FR-008–013 |
| V05 | Reload unanswered question seven; lose response after question commit; race two seventh/eighth requests; edit/reopen after seven published slots; resolve an outstanding gap through an intake correction | Same outstanding ID reloads; failed provider consumes no slot; resolved gap is not re-asked but its slot stays counted; maximum seven persistent slots across sessions and edits; remaining gaps stay open. FR-010/025 |
| V06 | Correct and confirm understanding, then immediately generate; edit a source after confirmation and attempt generation | Atomic correction confirmation is fresh; later change blocks generation until updated understanding is confirmed. Original source/correction both inspectable. FR-014 |
| V07 | Generate, inspect six categories and source/derivation links; mark intent CURRENT/DESIRED/BOTH/UNKNOWN; reject a supporting rule | Spanish categories include explicit empties; visible evidence only; dependent unsupported assertions become uncertain rather than remaining approved facts. FR-007–009/015–017 |
| V08 | A2 edits/approves A1's Feature; use two browsers to save from the same original token; race retry with a third change | Equal participant rights; stale save rejected; latest DB content preserved; local unsaved edits visible for explicit reconciliation; retry checks again. FR-019/021/025 |
| V09 | Edit requirements manually; regenerate then fail/reject; generate again then accept | Current human edits survive failure/rejection; proposal comparison available; acceptance requires fresh source/current baselines and enters Needs Review; old corrections remain retained. FR-015/016/018 |
| V10 | Edit source/current set during generation and before replacement acceptance | Stale completion/acceptance cannot replace current content; source changes invalidate prior approval even if proposal later rejected. FR-015/020 |
| V11 | Reconcile supported content with an unresolved question, acknowledge it, approve and export; separately try an included unsupported assertion | Supported set approves with visible unanswered gaps; acknowledgement cannot approve an unsupported dependent assertion. Approver/time/exact revision captured. FR-019/022 |
| V12 | Repeat export of same approval; compare all content; change each source/Screen/answer/understanding/requirement/gap type after approval | Same bytes for same approval; six headings/Spanish/intent/evidence preserved; changes require review/reapproval before further export. FR-020–024 |
| V13 | Race approval/edit/export; lose approval response then retry; submit a stale approval ID after another approval | One consistent approved projection or conflict; no mixed data or duplicate application; no silent newer export. FR-019/020/025 |
| V14 | B1 and anonymous users call direct routes, RPCs, and Storage paths; dual member supplies cross-Project source/Module IDs | No content leak or cross-link. Direct authority-state writes are denied even if the client bypasses UI. FR-021/028 |
| V15 | Revoke A1's membership with their JWT still valid; retry list/view/approve/export/Storage/run fetch and let an AI call finish | Future requests and AI publication denied; old signed-link bypass does not exist. Previously downloaded bytes cannot be recalled. FR-021, EC-09 |
| V16 | Confirm deletion, attempt all ordinary actions and in-flight AI finalization; inspect retained DB/files and exercise operator recovery in test stack | Feature immediately hidden, all accepted data retained, nothing publishes into deleted record; recovery clears effective approval and keeps question count. FR-029, SC-009 |
| V17 | Provider refusal, malformed/refabricated source refs, incomplete response, timeout, prompt injection, retry, lost network response | Spanish truthful failure/pending state; no false completion, approval, fabricated evidence, or lost saved input. FR-008/025 |
| V18 | First live analysis by participant; inspect notice, logs, client bundle and caching configuration | Spanish AI-processing notice shown; real data accepted; no secrets/source bodies in logs/client bundles/shared caches. FR-028 |
| V19 | Download approved document, open in a Markdown viewer, edit the local copy, inspect screenshot references as authorized/unauthorized users | Clear manual handoff, unchanged database, source IDs and authenticated links; no image embed claim or automatic GitHub/Spec Kit action. FR-023/024/026 |
| V20 | Keyboard-only sign-in, save, question, compare, approval, and export; observe focus/announced errors and Spanish labels | Entire workflow accessible without facilitator; controls/errors retain Spanish wording. FR-006/027 |

See [application contract](./contracts/application.md), [AI contract](./contracts/ai-service.md), [Markdown contract](./contracts/markdown-export.md), and [data model](./data-model.md) for exact payloads/invariants. Fixtures use valid image bytes, including a decoded PNG/JPEG at the size boundary, not merely forged MIME metadata.

## Human evaluation and measurable acceptance

Run all five stories with five representative Spanish-speaking users, each familiar with their single-Screen Feature and supplied screenshot. They perform the whole workflow themselves. Observers may measure and record problems but cannot operate the application or approve on behalf of participants.

- SC-001: All acceptance cases complete through the self-service workflow with Project isolation.
- SC-002: At least four of five finish within 20 minutes, including normal waiting, excluding voluntary breaks.
- SC-003: Every session respects one question, four options, seven lifetime slots and early stop.
- SC-004: Evidence review finds zero unsupported assertions represented as approved facts; intent is explicit.
- SC-005: All stale/unapproved/export tests deny authority and identify the human approver.
- SC-006: Every sampled Markdown matches the approved Spanish projection and six required sections.
- SC-007: Two engineering reviewers each find source, intent, and approval for five sampled requirements within two minutes per requirement without asking the original stakeholder.
- SC-008: At least four users rate the specified "own words/no formal terminology" statement >=4/5.
- SC-009: Every delete case hides ordinary access while retaining the recoverable complete record.

Record results separately from plans, with fixture/version IDs, model/prompt/schema versions, timing, gap/question counts, evidence failures, and user ratings. Use controlled authorized storage for real evaluation evidence; never commit real screenshots or business details as public test fixtures.

## Deployment and recovery smoke test

On the configured Vercel/Supabase environment verify sign-in, a real authorized Feature, >4.5 MB direct image transfer, source inspection, one AI operation, human review/approval, deterministic export, access revocation, and soft deletion. Confirm maxDuration/deadline settings and provider/model access. Test backup restoration or retained-record recovery in an isolated copy, including Storage objects; database backup alone is not proof that screenshot bytes are recoverable.

These are release verification steps, not deployments or tests performed by this planning command.
