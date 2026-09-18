# Research: SIFA Feature Discovery and Approved Requirements Export

**Date**: 2026-09-18  
**Reference**: [ARCHITECTURE.md](../../ARCHITECTURE.md), [spec.md](./spec.md), and constitution v1.0.0.  
**Result**: Planning unknowns resolved below. Provider-account availability and real performance are implementation/deployment checks, not measured results.

## R1. Application and dependency baseline

**Decision**: One Next.js 16 App Router application, React 19, TypeScript 5, Node.js 24.x, Tailwind CSS 4, shadcn/ui, Supabase JS v2/SSR, Zod, the OpenAI JS SDK, Sharp, and tus-js-client. Resolve compatible stable patches and commit package-lock.json when scaffolding. Use Vitest, Playwright, and PostgreSQL integration tests. Follow the architecture's server/features, server/requirements, server/exports, and server/ai layout; no generic repository framework.

**Rationale**: Preserves the supplied stack and one deployable application. Node handles image validation and the provider SDK; the current documented Next.js minimum is below the chosen runtime. Browser components handle forms and review, server code owns sensitive operations.

**Alternatives considered**: Express/microservices add a boundary the architecture excludes; an ORM is unnecessary for a small Supabase application with explicit transaction functions.

**Sources**: [Next.js installation](https://nextjs.org/docs/app/getting-started/installation), [Vercel Node versions](https://vercel.com/docs/functions/runtimes/node-js/node-js-versions). UI/test package choices are engineering defaults within the supplied stack.

## R2. Authorization, identity, and database writes

**Decision**: Supabase Auth invitation-based email/password access for the known participants; disable public signup. Project/Module/member provisioning is an operator setup step. Every participant has equal Feature rights. Verify the session with the Supabase SSR flow, then query live membership. All domain tables have RLS. Browser roles have no direct domain mutation grants; narrowly granted transactional PostgreSQL RPCs enforce command invariants. Server-only AI finalization/attachment validation use restricted RPC access and independently verify the initiating participant.

**Rationale**: Checking membership from current relational rows handles revocation without trusting stale JWT membership claims. Transactions provide atomic child writes and lifecycle transitions. Where a SECURITY DEFINER helper is necessary, use a fixed search_path, fully qualified tables, minimal grants, server-derived actor identity, and explicit authorization; never grant arbitrary state updates.

**Alternatives considered**: Creator-only permissions conflict with the spec; UI checks or broad service-role CRUD bypass the required boundary.

**Sources**: [Supabase SSR identity](https://supabase.com/docs/guides/auth/server-side/creating-a-client?queryGroups=framework&framework=nextjs), [Supabase RLS](https://supabase.com/docs/guides/database/postgres/row-level-security).

## R3. Upload and screenshot access

**Decision**: Interpret 10 MB as 10,000,000 bytes, inclusive, consistently in UI, Storage, and tests. Use a private screenshot bucket. A small prepare command allocates an upload intent and an opaque immutable object path. Browser uploads directly to Storage using authenticated resumable TUS; no upsert or client update/delete. Pending objects are unreadable to participants and do not count as Sources. A finalize command downloads server-to-server, checks byte count, signature and MIME, fully decodes PNG/JPEG with Sharp, hashes the original, then publishes Source/Attachment rows transactionally. Existing sources remain intact on failure.

**Rationale**: Vercel's documented 4.5 MB request/response cap cannot carry the spec's 10 MB image payload. Supabase recommends resumable upload for larger files. Header parsing alone does not detect all corrupt images.

**Alternatives considered**: Multipart Next.js upload and image-proxy download fail the size requirement. Public or participant-facing signed download URLs cannot enforce fresh membership on every read. Use authenticated Storage downloads with live membership, accepted-attachment, and active-Feature RLS checks; render a temporary browser Blob URL and release it on navigation. Disable shared caching/public image optimization for private content. Already received bytes cannot be recalled.

Resource limits yield a Spanish retry/processing failure, never a silent claim that a valid image was analyzed. Do not introduce a new product pixel-dimension limit without reconciling the specification. Retain accepted files on Feature soft deletion; rejected/unfinalized uploads never become evidence and may be cleaned as explicitly uncommitted technical objects.

**Sources**: [Vercel limits](https://vercel.com/docs/functions/limitations), [Supabase resumable uploads](https://supabase.com/docs/guides/storage/uploads/resumable-uploads), [private downloads](https://supabase.com/docs/guides/storage/serving/downloads), [bucket restrictions](https://supabase.com/docs/guides/storage/buckets/fundamentals), [Sharp input metadata](https://sharp.pixelplumbing.com/api-input/), [Sharp decoder options](https://sharp.pixelplumbing.com/api-constructor/).

## R4. Evidence, concurrency, and authority

**Decision**: Append-only sources and immutable requirement-set revisions; a Feature points to its current revision and current approval. Separate edit_revision from source_revision. Commands compare the reviewed revision and lock the Feature within a short transaction. Regeneration keeps its source, understanding, and current-set baselines; completion/acceptance reject stale baselines. Corrections to understanding append a human Source and confirm the corrected summary against the resulting source revision in one transaction.

**Rationale**: Original Spanish wording and human changes survive replacement. A correction does not accidentally invalidate its own confirmation. Approval binds every exportable field, not just requirement text; export materializes one consistent approved projection before rendering.

**Alternatives considered**: Last-write-wins loses edits; overwriting an AI JSON blob loses evidence; independently reading mutable rows can mix approved and unapproved content. Schema-valid evidence links alone do not prove semantic support: review and evaluation remain necessary.

**Sources**: [PostgreSQL row locks](https://www.postgresql.org/docs/current/explicit-locking.html), [transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html).

## R5. AI contract, model, and evidence quality

**Decision**: Keep the architecture's three server operations: analyzeFeature, getNextInterviewQuestion, generateRequirements. Use Responses with strict structured outputs plus runtime/semantic validation. Rebuild context from PostgreSQL and authorized screenshots. Use configurable OPENAI_MODEL, initially gpt-4.1-2025-04-14 as a reproducible evaluation baseline; its documentation lists image input, Responses, and structured-output support. Release requires passing the Spanish evidence evaluation and confirming account access. This is not a claim that it is the newest or optimal model.

**Rationale**: A pinned baseline makes evaluation reproducible. The model emits local references resolved against the supplied source manifest, never database IDs or authority. Handle refusal, incomplete response, malformed output, and unavailable evidence as failures or uncertainties. Structured outputs constrain shape but can still contain factual mistakes.

**Alternatives considered**: Free-form parsing weakens validation; tools/agents, RAG, multi-provider infrastructure, and automatic UI-element detection violate scope. Screenshot interpretation describes visible evidence only, without producing element coordinates/selectors or inferred permissions.

**Sources**: [Structured outputs](https://developers.openai.com/api/docs/guides/structured-outputs), [GPT-4.1 capabilities and snapshots](https://developers.openai.com/api/docs/models/gpt-4.1), [image input and limitations](https://developers.openai.com/api/docs/guides/images-vision).

## R6. Real-data processing and bounded execution

**Decision**: Show a Spanish processing notice before first analysis and record its displayed version per participant/Project. Use store:false and no provider conversation/thread state; send images server-to-server as data URLs. Keep secrets server-only, omit business text/images from operational logs, and store only necessary run metadata. Provider abuse-monitoring retention can still apply; store:false is not a zero-retention guarantee. Do not claim account-specific retention controls are enabled.

**Rationale**: The application accepts real SIFA data while making AI processing explicit. Source-of-truth and recovery stay in PostgreSQL.

**Alternatives considered**: Requiring anonymized inputs contradicts FR-028. Provider conversation storage duplicates authoritative application state.

**Sources**: [OpenAI data controls](https://developers.openai.com/api/docs/guides/your-data), [image input](https://developers.openai.com/api/docs/guides/images-vision).

**Execution decision**: Node route handlers with a 300-second deployment budget and a 240-second application/provider deadline; configure SDK retries within that total. Persist run IDs, idempotency keys, leases, captured revisions, prompt/schema/model versions, and status. Poll saved status after disconnection; expired leases become retryable. Do not hold DB locks over provider calls or rely on fire-and-forget work. Finalization rechecks authorization, deletion, and all input baselines. Slow/unavailable AI preserves saved intake and current requirements.

**Alternative considered**: A queue/worker service adds infrastructure before the bounded POC needs it. Revisit only if measured accepted use cases cannot finish within the budget.

**Sources**: [Vercel duration](https://vercel.com/docs/functions/configuring-functions/duration), [Next.js after limits](https://nextjs.org/docs/app/api-reference/functions/after).

## R7. Interview counting, deletion, and handoff

**Decision**: Generate a candidate before reserving a question ordinal. A locked commit publishes at most one outstanding question with a lifetime ordinal 1–7. A lost response reloads the same committed question. Failure consumes no ordinal; skipped/unanswered published questions remain counted. Idempotent retries always recheck authorization.

**Decision**: Soft deletion commits the Feature tombstone immediately and retains all accepted child records/files. Ordinary RLS and commands exclude deleted ancestors, including AI completion. Recovery is an operator procedure with a validation exercise; there is no end-user restore or permanent-purge feature. Retention duration and future purge policy remain explicitly deferred per the specification.

**Decision**: Render Markdown deterministically from the exact approved relational snapshot, with all six Spanish sections, identified screenshot references, unresolved items, and no AI call. User editing of the downloaded file has no database effect.

**Rationale**: These choices enforce the clarified product boundaries under concurrency and retries without adding excluded capabilities.

**Alternatives considered**: Counters in browser/AI memory reset on retries; delayed visibility removal lets deleted Features remain exportable; AI export rewriting changes approved authority.

**Source**: [spec.md](./spec.md), particularly FR-010, FR-015, FR-019–029.
