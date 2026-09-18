# Data Model

**Scope**: Relational PostgreSQL model for [spec.md](./spec.md), following [ARCHITECTURE.md](../../ARCHITECTURE.md). This is a schema design, not a migration.

## Shared rules and revision tokens

IDs are application/database UUIDs; timestamps are database-generated UTC. All Feature-owned tables carry project_id and feature_id, with composite ownership foreign keys so cross-Project or cross-Feature references are impossible. Requirement-to-requirement links also share a set_revision_id. RLS checks live membership and the active ancestor, including on retained historical records.

A ReviewToken contains featureId, editRevision, sourceRevision, currentSetRevisionId (nullable), and understandingId (nullable). The server compares the entire relevant baseline, not merely a browser-supplied state enum.

- **edit_revision** advances on saved changes to current business content, source selection, understanding, requirements, Open Questions, replacement acceptance, and deletion.
- **source_revision** advances when an intake/source, Screen label/image association, interview answer, or identified human correction/decision changes.
- AI run bookkeeping, publishing an interview question, and preparing/rejecting a replacement do not themselves advance the current business revision or invalidate approval.
- A set revision is immutable. Editing produces a new revision and moves the current pointer; old human-edited revisions remain retained.
- Approval records the exact token/content it approves. Effective Approved state also requires a valid current approval pointer and matching business revisions.
- Newly confirmed understanding references the resulting source_revision, including any correction made in that same command.

## Containment and access

| Table | Fields and constraints | Relationships |
|---|---|---|
| projects | id, name, created_at | Contains Modules and memberships |
| project_memberships | project_id + user_id primary key, joined_at, revoked_at | auth.users identity; live, non-revoked row grants equal rights |
| modules | id, project_id, name, active | Composite id/project key; provisioning only in POC |
| features | id, project_id, module_id, current Spanish name/users/purpose/workflow/rules, users_status, rules_status, state, edit_revision, source_revision, current_set_revision_id?, current_understanding_id?, current_approval_id?, deleted_at?, deleted_by?, created_by, timestamps | Module must share Project; current pointers must belong to this Feature |

Feature states: DRAFT, NEEDS_REVIEW, APPROVED. Deletion is an independent tombstone, not a replacement for retained lifecycle. users_status is KNOWN/UNKNOWN/UNANSWERED; rules_status adds NONE_KNOWN. Current intake values project immutable Source records; original source strings preserve exact spelling and accents. A saved partial Draft may lack analysis-required values.

Projects and Modules are provisioned before use. Actors are plain Spanish intake context, not app permissions. Project Actor/Glossary CRUD is deferred.

## Sources, Screens, and uploads

| Table | Fields and constraints |
|---|---|
| screens | id, project_id, feature_id, current label, position, detached_at?; stable identity despite duplicate labels |
| sources | id, ownership, kind, field_key?, original_text?, attachment_id?, screen_id?, author_id, created_at, supersedes_source_id?, introduced_source_revision; append-only |
| feature_source_links | feature_id, source_id, active_from_revision, inactive_from_revision?; preserves which immutable evidence was active at each revision |
| upload_intents | id, ownership, screen_id, uploader_id, unique object_path, declared bytes/MIME/name, status, expires_at, created_at; no evidence authority |
| attachments | id, ownership, screen_id, upload_intent_id unique, immutable object_path, original filename, detected MIME, bytes, sha256, width, height, validated_at, accepted_by |
| source_assessments | id, source_id, ai_run_id? or human actor, readability AVAILABLE/LIMITED/UNREADABLE, explanation; interpretation, not an original Source |

Source kinds: INTAKE, SCREENSHOT, INTERVIEW_ANSWER, HUMAN_CORRECTION, HUMAN_DECISION. AI interpretations live in understanding/run/assessment records and cannot become independent evidence merely by citing themselves.

Screenshot publication requires bytes <=10,000,000, valid PNG/JPEG signature and MIME, and successful full decode. Invalid files never create an accepted attachment/Source. Visually blurry but decodable images can be accepted and flagged as limited evidence. Browser Storage access uses the exact intent/attachment mapping plus current membership, not just path prefixes.

Upload states: PREPARED -> UPLOADED (observed server-side) -> ACCEPTED, or REJECTED/EXPIRED. Only finalization creates an accepted Source and advances revisions. Finalization is idempotent; version conflict leaves an uncommitted intent/object available to retry after reconciliation. Original bytes cannot be overwritten by clients. Detaching/replacing accepted evidence retains original records/files and invalidates approval.

## Interview and understanding

| Table | Fields and constraints |
|---|---|
| interview_questions | id, ownership, ordinal 1..7, gap_key, prompt_es, answer_mode SINGLE/MULTIPLE/FREE_TEXT, based_on_source_revision, published_at, state OUTSTANDING/ANSWERED/SKIPPED/RESOLVED_BY_SOURCE, resolved_by_source_id? |
| interview_options | id, question_id, position 1..4, label_es; unique question/position |
| interview_answers | id, question_id, author_id, disposition ANSWERED/UNKNOWN/SKIPPED, context_es?, source_id, created_at, supersedes_answer_id?; append-only revisions |
| interview_answer_choices | answer_id, option_id; option must belong to answer's question |
| understanding_summaries | id, ownership, based_on_source_revision, purpose_es, users_es, workflow_es, rules_es, created_by_run?, corrected_by?, confirmed_by?, confirmed_at?; new content means new row |
| understanding_items | id, understanding_id, category, text_es, behavior_intent, support_state, position |
| understanding_item_sources | item_id, source_id, support description; same Feature |
| understanding_gaps | id, understanding_id, gap_key, question_es, conflict_es?, status |
| understanding_gap_sources | gap_id, source_id; both sides of a source conflict are retained |

Unique (feature_id, ordinal); a partial unique constraint permits only one OUTSTANDING question per Feature. The published row is the durable presentation slot: persist before sending it to the browser. If delivery is lost, return that same slot; no new question may be issued until it is answered/skipped. Count all retained published slots, including skipped/unanswered; no delete/reopen/edit resets the ceiling.

Candidate generation consumes no slot. Under the Feature lock: return an existing still-relevant outstanding question (including ordinal seven), otherwise check captured source baseline and count <7, then commit one question/options. If changed evidence resolves the outstanding gap, mark the question RESOLVED_BY_SOURCE with its supporting Source link; retain the counted ordinal and do not ask it again. Question failure or stale result consumes zero. Multiple-choice questions have 1–4 options; free-text may have zero. Optional context is always available. Blank/unknown/skipped answers supply no business fact.

Analysis requires Module, name, one accepted screenshot, nonblank purpose/workflow, and an explicit response for users/rules. Unknown is valid; unanswered is missing. Generation requires confirmed current understanding with based_on_source_revision == features.source_revision.

A correct-and-confirm command checks the old token, appends the human correction Source, advances source_revision, stores the corrected summary against that new revision, confirms it, and returns the new token atomically. It never confirms an obsolete summary and then invalidates it with its own correction.

## Requirements and uncertainty

| Table | Fields and constraints |
|---|---|
| requirement_sets | id, ownership, origin INITIAL/REGENERATION, disposition CURRENT/PROPOSED/REJECTED/SUPERSEDED, generated_source_revision, generating_understanding_id, base_current_set_revision_id?, created_by_run |
| requirement_set_revisions | id, set_id, revision_number, created_by, created_at, previous_revision_id?; immutable |
| requirements | id, set_revision_id, logical_key, type, text_es, behavior_intent, disposition INCLUDED/REJECTED/UNCERTAIN, position |
| requirement_sources | requirement_id, source_id, support_kind and explanation; actual evidence, same Feature |
| requirement_links | from_requirement_id, to_requirement_id, relation DERIVES_FROM/SUPPORTED_BY; same revision, no self/cyclic derivation |
| open_questions | id, ownership, set_revision_id, logical_gap_key, question_es, context_es, resolution OPEN/RESOLVED, resolution_source_id?, position |
| open_question_requirements | question_id, requirement_id; affected assertions |
| open_question_sources | question_id, source_id; evidence/conflict basis |
| requirement_change_sources | set_revision_id, requirement logical_key, human source_id, change_kind; provenance of edits/rejections/intent decisions |

Requirement types exactly follow the reference: USER_STORY, BUSINESS_RULE, FUNCTIONAL_REQUIREMENT, ACCEPTANCE_CRITERIA. behavior_intent is CURRENT, DESIRED, BOTH, UNKNOWN and renders in Spanish. UNKNOWN intent never becomes desired by default. Each behavior-bearing understanding item and requirement carries intent.

All six output categories are represented even if no rows exist for a category. The UI/serializer emits the defined empty statement; it never creates a fabricated requirement.

A rejected/unsupported rule cannot silently support an included dependent requirement. Dependency invalidation marks affected assertions UNCERTAIN/PENDING until reviewed. An included assertion needs real supporting Source links and human support review; an acknowledgement checkbox cannot substitute for evidence. Explicit human business statements can be Sources. Acknowledged unresolved intent may remain UNKNOWN without asserting a desired migration decision.

Human review edits create new set revisions and identified correction Sources. Consequently they update both source/edit revisions and invalidate prior approval; reconfirmed understanding is required before later generation. Approval itself uses a reconciliation receipt, below, so a user need not regenerate or repeat AI just to approve their supported edits.

## Reconciliation and approval

| Table | Fields and constraints |
|---|---|
| review_receipts | id, ownership, set_revision_id, source_revision, edit_revision, understanding_id, reviewed_by, reviewed_at; immutable attestation that current content and source changes were inspected/reconciled |
| review_receipt_items | receipt_id, requirement_id, support_review SUPPORTED/UNSUPPORTED, reviewed_source_ids via relational links; attests support without mutating immutable requirement rows |
| approvals | id, ownership, set_revision_id, receipt_id, source_revision, edit_revision, understanding_id, approved_by, frozen approver display identity, approved_at, project/module/Feature labels, actor context, purpose/workflow/rules context, content_hash; immutable |
| approval_sources | approval_id, source_id, stable display identifier/description and frozen evidence limitations; source text/bytes remain immutable |
| approval_screens | approval_id, screen_id, frozen label and ordering, attachment_id |
| approval_open_questions | approval_id, open_question_id, acknowledged_by, acknowledged_at |

Approval binds the entire exportable projection: context, Screens, immutable sources and descriptions, requirement revision/links/classifications, uncertainties, and acknowledging human. Relational snapshot fields prevent a future Module label edit from silently changing old approved bytes. content_hash verifies the canonical projection; the relational rows, not the hash alone, retain its content.

A review receipt is valid only for the current token. New source/requirement/understanding/Open Question changes invalidate it. Approval requires the exact receipt, reviewed token, all unresolved question IDs acknowledged, and no INCLUDED item with missing/unsupported evidence or unresolved factual dependencies. Uncertainty remains in Open Questions/review markers, outside authoritative assertions.

## Commands, AI runs, and transactions

| Table | Fields and constraints |
|---|---|
| command_receipts | actor_id, command_id, request_fingerprint, ownership, result identifiers; unique actor/command; reuse with different payload rejected |
| ai_runs | id, ownership, actor_id, operation, command_id, captured source/edit/current-set/understanding baselines, model/prompt/schema versions, status, lease_expires_at, usage metadata, sanitized error code, result IDs |
| ai_notice_receipts | project_id, user_id, notice_version, displayed_at; evidence that the Spanish processing notice was shown before first analysis |

AI status: RUNNING -> SUCCEEDED/FAILED/STALE/TIMED_OUT. A lease expires below the route budget. Repeated calls return saved status/results only after fresh membership/deletion checks; completed runs cannot reapply domain writes. JSONB may hold non-authoritative provider diagnostics; avoid retaining unnecessary raw sensitive output.

Every command uses consistent lock order (membership, then Feature, then children), checks live authorization and deletion, compares expected revisions, writes all changes, and records its receipt atomically. Membership revocation follows the same membership lock so a successful action has a defined authorization instant. Protected SQL functions validate membership/ownership even if the application already did so. No provider call or file transfer holds a database transaction open.

AI start captures an immutable input manifest; finalization reacquires authorization/Feature locks and compares input baselines. Generation also compares current-set/understanding IDs. Any mismatch makes the run stale without replacing current content. Proposal acceptance rechecks the same baselines and the revision actually reviewed.

## Lifecycle and deletion

| Trigger | Current state/content | Result |
|---|---|---|
| Partial intake saved | No set | DRAFT |
| First successful generation | Fresh confirmed understanding; no current set | New current revision, NEEDS_REVIEW |
| Regenerate / reject replacement | Any current set | Preserve current set/state/approval; proposal tracked separately |
| Accept fresh replacement | Source/current-set baselines unchanged | Current pointer changes, NEEDS_REVIEW; clear approval and old receipts |
| Explicit approval | Fresh receipt, supported included content, acknowledged gaps | APPROVED, immutable current approval |
| Source/intake/Screen/answer/understanding/requirements/gap change | APPROVED | NEEDS_REVIEW; clear current approval/receipt, retain historical approval |
| Same business change before any generation | DRAFT | Remain DRAFT |
| Confirmed soft delete | Any state | Immediate deleted_at/deleted_by; deny normal access and all AI finalization |

A restored Feature stays at least NEEDS_REVIEW if it had a set, clears current approval, advances edit_revision, and retains question history. Recovery is an operator-only documented procedure for the retained data, not a public endpoint. No cascading hard-delete or scheduled purge of accepted Feature records/files is included.

## Export consistency

In one transaction, lock membership/Feature, verify expected approval ID and ReviewToken against current state, and materialize all approved projection rows consistently. End the transaction, then render those immutable values. An edit committed before this snapshot rejects a stale export; one committed afterward affects future requests, not the already-authorized bytes. No mixed-revision content or AI rewriting is possible. See [export contract](./contracts/markdown-export.md).
