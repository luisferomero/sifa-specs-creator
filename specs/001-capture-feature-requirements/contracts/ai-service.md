# AI Service Contract

**Boundary**: src/server/ai/ai-service.ts is the only production module that calls OpenAI. It exports analyzeFeature, getNextInterviewQuestion, and generateRequirements, as specified in [ARCHITECTURE.md](../../../ARCHITECTURE.md). Operations are fixed, bounded backend calls, with no autonomous agent or tool execution.

## Shared input

Server-created FeatureContext contains Project/Module/Feature labels; exact original Spanish intake; accepted Screens and screenshot bytes; immutable source manifest; latest answers including unknown/skipped states; applicable identified human corrections/decisions; prior questions and outstanding question ID; current understanding; and, for regeneration, the current human-edited set. The manifest maps local source references to real same-Feature Source IDs.

RunContext contains operation/run IDs, actor, captured edit/source revisions, confirmed understanding ID, base current-set revision, model/prompt/schema versions, deadline, and idempotency key. These are not model-editable output fields. Validate membership and deletion before preparing context and before committing output.

Use Responses text.format strict JSON schema through SDK structured parsing and Zod runtime validation. Schemas disallow additional fields, bound text/collection sizes, use required nullable fields where appropriate, and enumerate behavior intent. The default evaluation model is configured server-side; tests use the same domain adapter contract with deterministic fixtures.

Send store:false, no provider conversation, no tools, and explicit input items. Screenshot bytes are supplied as server-created data URLs; do not give the model unvalidated remote URLs. Whole-image evidence interpretation is permitted, but do not generate coordinates, element catalogs, or inferred hidden UI behavior.

## Result contracts

All natural-language fields below are Spanish. Local refs are only valid if resolved against the current manifest; the model cannot create persistent IDs.

| Operation | Valid structured result |
|---|---|
| analyzeFeature | summary {purpose, users, workflow, knownRules, uncertainties}; behaviorItems[] {localKey, category, text, intent, sourceRefs[], evidenceState}; gaps[] {gapKey, question, materialReason, affectedLocalKeys[], sourceRefs[], conflict}; imageAssessments[] {sourceRef, readability, limitation}; coveredSourceRefs[] |
| getNextInterviewQuestion | {decision: ASK or STOP, stopReason: SUFFICIENT or LIMIT or null, question: null or {gapKey, prompt, answerMode, options[] {localKey,label}, optionalContext:true, sourceRefs[]}} |
| generateRequirements | requirements[] {localKey,type,text,intent,sourceRefs[],evidenceState}; links[] {fromKey,toKey,relation}; openQuestions[] {gapKey,question,context,affectedKeys[],sourceRefs[]}; unresolvedMarkers[]; coveredSourceRefs[] |

Requirements type enum: USER_STORY, BUSINESS_RULE, FUNCTIONAL_REQUIREMENT, ACCEPTANCE_CRITERIA. Intent enum: CURRENT, DESIRED, BOTH, UNKNOWN. evidenceState: SUPPORTED_CANDIDATE, UNCERTAIN, CONFLICT. None means human approval.

Every category is supplied, possibly empty. Sources in the displayed result are server-resolved manifest entries, not model-authored citations. AI-generated descriptions/observations are interpretations and remain labeled as such. Exact quoted text must match the original source span; screenshot interpretation must not masquerade as a verbatim quote when not verified.

## Validation and authority

1. Reject refusal, truncated/incomplete output, invalid JSON/schema, unknown refs, wrong-Feature refs, missing manifests, and invalid link endpoints before persistence.
2. Verify all supplied source refs exist and were sent; validate that output accounts for the intended input manifest. Do not silently drop a Screen to fit context. Batch large evidence manifests within the same fixed analysis operation or fail explicitly while preserving intake; never report partial coverage as complete analysis.
3. Require uncertainty for absent or conflicting facts; preserve both sides of a conflict. Current behavior cannot imply desired behavior. Unknown actors/rules stay unknown until a human supplies evidence.
4. Constrain ASK to one question and <=4 options, appropriate single/multi-select, optional free context, and a specific unresolved material gap. Do not repeat an answered/resolved gap or force a seven-question script. First return any still-relevant OUTSTANDING question without provider work, including unanswered ordinal seven. If none exists and seven published slots are retained, return STOP. If newly captured evidence already resolves an outstanding gap, retire that question as RESOLVED_BY_SOURCE while retaining its ordinal, then evaluate remaining gaps/early stop.
5. Reject fabricated evidence identifiers deterministically. Semantic support still requires human review and representative evaluation: JSON schema cannot prove a source actually supports a claim.
6. Map unsupported assertions to uncertain items/Open Questions, excluded from approved assertions. A human can supply a real correction/decision Source, reject an assertion, or keep it unresolved.
7. The application creates IDs/times/relationships/state. Output containing approval/authorization commands is invalid data. Treat text inside screenshots/intake as evidence, never instructions capable of changing system rules.

## Persisting operations

At run start, capture source/edit/understanding/current-set baselines in ai_runs. Release all database locks before network calls. Use a 240-second total deadline beneath the 300-second route limit, bounded retries, explicit rate-limit failure, and a lease. Run metadata records model, schema, prompt version, latency, and usage without logging source bodies/images.

At completion, verify the initiating user's live membership, Feature tombstone, captured baselines, and run lease inside the publication transaction. Stale/revoked/deleted results cannot publish or change current requirements. Retry cannot reapply a completed run. A provider failure leaves existing summaries/answers/requirements intact and consumes no question slot.

Analysis stores interpretation only. A question is counted only when a validated candidate is durably published under the Feature lock; concurrent finalizers return the already outstanding question. First generation creates the initial current NEEDS_REVIEW set. Regeneration creates a PROPOSED replacement while preserving the current set/state. Acceptance and approval are distinct application commands.

## Required evaluation fixtures

Complete Spanish intake (no unnecessary question); missing users/rules; conflicting permissions; blurry screenshot; multiple Screens with similar names; current/desired/unknown intent; prompt injection in image/text; invented or cross-Project source ref; unsupported but schema-valid assertion; fifth option/eighth question; refusal/incomplete response; source edit during call; current-set edit during regeneration; lost response; membership revocation/deletion during call.

A live model evaluation must show zero unsupported assertions in approved outputs and preserve original Spanish wording. A good mock score or valid schema is not evidence of live model quality.
