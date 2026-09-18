# Feature Specification: SIFA Feature Discovery and Approved Requirements Export

**Feature Branch**: `dev` (existing branch; no branch-creation hook configured)

**Created**: 2026-09-18

**Status**: Draft

**Input**: The user-provided Project Initialization Foundation describes a web application
where Spanish-speaking SIFA stakeholders document legacy Features using screenshots and plain
language, clarify material gaps with AI, review and approve structured requirements, and export
Markdown for manual use with GitHub Spec Kit.

**Governing document**: [SIFA Specs Creator Constitution v1.0.0](../../.specify/memory/constitution.md).

This specification covers one end-to-end POC. All stories below are required for POC acceptance;
priorities order delivery rather than making lower-priority constitutional requirements optional.
The POC MUST be fully functional for authorized end users: they complete the workflow directly,
without a facilitator performing, approving, or exporting work on their behalf.

## Clarifications

### Session 2026-09-18

- Q: Who should be able to edit and approve a Feature in the SIFA documentation app? → A: Every
  identified user authorized for the Project can edit and approve any Feature in that Project,
  regardless of who created it. Viewing and approved exports are shared within the Project.
- Q: What should happen if two users edit the same Feature and one saves before the other? → A:
  Block the outdated save without overwriting the latest saved content, preserve the second
  user's unsaved edits, and require reviewing the latest version before retrying.
- Q: When AI regenerates requirements that users have edited, should it keep the existing
  requirements until someone reviews and accepts the proposed replacement? → A: Yes. Keep the
  existing requirements and human edits unchanged until a user reviews and explicitly accepts
  the replacement. The accepted replacement enters Needs Review and requires separate approval
  before export.
- Q: Should the initial POC use only test data or screenshots and descriptions anonymized before
  upload? → A: No. Authorized end users can upload real SIFA screenshots and business details and
  use the application as a production-like end-user workflow.
- Q: Should authorized Project users be able to permanently delete a Feature and its real
  screenshots and business details, or should deletion require a separate administrator process?
  → A: Deletion is handled as a background soft delete. It removes the Feature from normal end-user
  use while retaining its data for recovery rather than permanently erasing it.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Capture and Resume Business Knowledge (Priority: P1)

As an authorized SIFA stakeholder, I can select a Module, describe a Feature in my own Spanish
words, attach its Screens, and return later so my knowledge is captured without a discovery meeting
or facilitator-operated workflow.

**Why this priority**: Reliable intake provides immediate value even before AI-generated
requirements are available.

**Independent Test**: An authorized end user creates, saves, closes, and reopens a Feature using
an available Project and Module. Their wording and screenshots remain available without AI use or
staff intervention.

**Acceptance Scenarios**:

1. **Given** an authorized participant and an available Module, **When** they provide the minimal
   intake and save, **Then** a Draft Feature belongs to that Module and Project, and reopening it
   preserves the original Spanish text and screenshots. All controls and feedback are in Spanish.
2. **Given** incomplete intake, **When** the participant saves, **Then** the partial Draft is kept;
   requesting analysis identifies missing required intake without discarding entered information.
   An explicit unknown answer is accepted for users or rules and becomes a clarification gap.
3. **Given** a Feature involving two Screens, **When** the participant adds labeled Screens and
   valid PNG and JPEG screenshots up to 10 MB per file, **Then** the images are accepted and both
   Screens remain grouped under the same Feature with distinguishable sources.
4. **Given** an unsupported, corrupt, or oversized upload, **When** it is submitted, **Then** a
   Spanish message explains the problem and accepted file limits; existing saved content remains.
5. **Given** a person outside the authorized Project participants, **When** they attempt to view,
   change, approve, or export its Feature, **Then** access is denied and its content is not exposed.
6. **Given** two participants editing the same saved Feature, **When** one saves and the other
   attempts to save their now-outdated edits, **Then** the second save is blocked, the latest
   saved content remains unchanged, and the second participant's unsaved edits remain available
   with Spanish guidance to review the latest version and reconcile their edits before retrying.
7. **Given** an authorized end user, **When** they follow the documented workflow, **Then** they
   can create, analyze, clarify, review, approve, and export their Project's Features directly
   without a facilitator operating the application for them.
8. **Given** an authorized end user with real SIFA screenshots and business details for their
   Project, **When** they submit them through the normal workflow, **Then** the application
   accepts and processes that real Project data without requiring anonymization or test-data
   substitution, while keeping it available only to authorized Project participants.
9. **Given** an authorized Project participant deletes a Feature, **When** the deletion is
   confirmed, **Then** the Feature, its Screens, sources, requirements, Open Questions, and
   approvals are removed from normal end-user lists and actions without permanent erasure; their
   record remains retained as a background soft deletion for recovery.

---

### User Story 2 - Review Understanding and Approve Requirements (Priority: P1)

As an authorized Project participant, I can correct what AI understood, inspect proposals, and
explicitly approve requirements for any Feature in my Project, including another user's Feature,
so automated interpretation never becomes authority without human review.

**Why this priority**: Human review is the central trust boundary between discovery and an
engineering handoff.

**Independent Test**: Using a prepared Feature created by another Project participant with
sufficient source information, a participant corrects the understanding summary, generates
proposals, and approves a reviewed requirements set.

**Acceptance Scenarios**:

1. **Given** sufficient captured information, **When** analysis finishes, **Then** the participant
   sees a Spanish summary of purpose, users, workflow, known rules, and uncertainties; requirements
   are not generated until the participant confirms that understanding.
2. **Given** an incorrect summary, **When** the participant corrects it, **Then** original input
   remains intact and the correction becomes an identified human source for subsequent generation.
3. **Given** confirmed understanding, **When** requirements are generated, **Then** the current set
   enters Needs Review and contains User Stories, Business Rules, Functional Requirements,
   Acceptance Criteria, Open Questions, and Sources. Empty sections state that nothing is captured
   rather than asserting nonexistent business facts.
4. **Given** a proposal requiring correction in a Feature created by another Project participant,
   **When** an authorized participant edits or rejects it, **Then** the action is allowed and the
   reviewed content preserves the source and human change; rejected assertions do not appear as
   approved requirements.
5. **Given** reviewed requirements with supported assertions in another participant's Feature,
   **When** an identified user authorized for the Project explicitly approves the current set,
   **Then** it becomes Approved without creator-only or Product Lead restrictions and records
   who approved it, when, and which content was approved.
6. **Given** an unresolved question, **When** the participant approves the supported content and
   acknowledges the remaining gap, **Then** the question remains visibly unresolved; neither its
   missing answer nor a dependent unsupported assertion becomes approved business behavior.
7. **Given** an Approved Feature, **When** intake business content, screenshots, interview answers,
   confirmed understanding, or requirements change, **Then** its current set returns to Needs
   Review and cannot be exported as approved until the changes are reconciled and approved again.
8. **Given** confirmed understanding, **When** its source information changes before generation,
   **Then** an updated summary must be reviewed and confirmed before new requirements are generated.
9. **Given** existing requirements containing human edits, **When** regeneration succeeds,
   **Then** the new proposal is available for review alongside the unchanged current requirements;
   rejecting the proposal leaves the current requirements and their human edits intact.
10. **Given** a reviewed replacement and unchanged source information and current requirements,
    **When** an authorized Project participant explicitly accepts the replacement, **Then** it
    becomes the current set in Needs Review and requires separate approval before export.
11. **Given** a proposed replacement, **When** another participant changes its sources or the
    current requirements before acceptance, **Then** accepting that outdated proposal is blocked
    until it is refreshed and reviewed against the latest content.

---

### User Story 3 - Export an Approved Engineering Handoff (Priority: P1)

As a stakeholder or engineering participant, I can download an approved Spanish Markdown
document containing the business context, requirements, and evidence for manual specification work.

**Why this priority**: The approved document is the POC's final deliverable and must represent the
reviewed content faithfully.

**Independent Test**: Export a prepared Approved Feature and compare the resulting document with
its approved content, evidence references, behavior classifications, and remaining questions.

**Acceptance Scenarios**:

1. **Given** an Approved Feature, **When** export is requested, **Then** a readable `.md` document
   contains its context and all six required sections, preserving Spanish wording, approval
   details, behavior classifications, and identifiable sources without AI rewriting.
2. **Given** a Draft or Needs Review Feature, **When** export of approved requirements is requested,
   **Then** it is prevented and a Spanish message identifies the outstanding review or approval.
3. **Given** an Approved Feature with acknowledged Open Questions, **When** it is exported,
   **Then** the questions and any dependent uncertainties remain explicitly unresolved.
4. **Given** a failed download, **When** the participant retries, **Then** the saved approved content
   remains available and export can be retried without repeating AI analysis or approval.
5. **Given** a downloaded document, **When** someone edits that file, **Then** the application's
   approved record is unchanged; export does not publish to GitHub or execute Spec Kit.

---

### User Story 4 - Resolve Important Gaps in a Short Interview (Priority: P2)

As a stakeholder, I receive only relevant clarification questions, one at a time, so I can fill
important gaps without a long questionnaire or repeated explanations.

**Why this priority**: Focused clarification improves incomplete intake while protecting the
minimal stakeholder effort required by the POC.

**Independent Test**: Analyze prepared Features with complete, incomplete, and conflicting inputs;
verify question relevance, answer handling, early stopping, and the per-Feature ceiling.

**Acceptance Scenarios**:

1. **Given** a material gap specific to the Feature, **When** clarification starts, **Then** one
   Spanish question is presented with at most four selectable answers, optional free-text context,
   and multi-select when multiple supplied answers can apply together.
2. **Given** earlier answers resolve the important gaps, **When** the next step is requested,
   **Then** the interview stops before seven questions and does not repeat supplied information.
3. **Given** seven questions have been presented, including unanswered or skipped questions across
   sessions, **When** the participant continues, reopens, or edits the Feature, **Then** no eighth
   question is asked; remaining gaps become Open Questions or review markers.
4. **Given** adequate initial information, **When** analysis runs, **Then** the interview is skipped
   and the participant proceeds to review the AI understanding.
5. **Given** the participant cannot answer a question, **When** they leave it unanswered or skip,
   **Then** no answer is invented and the gap remains visible without forcing a selection.
6. **Given** a failed analysis or interrupted interview, **When** the participant returns and
   retries, **Then** saved inputs, answers, and the question count remain; failure or reloading an
   existing question does not consume an additional question or claim completion.

---

### User Story 5 - Inspect Evidence and Distinguish Migration Intent (Priority: P2)

As a stakeholder or technical reviewer, I can see why a requirement exists and whether it records
current or desired behavior, so the new system does not silently inherit every legacy behavior.

**Why this priority**: Evidence and explicit intent make proposed requirements reviewable and
protect migration decisions from unsupported assumptions.

**Independent Test**: Review a prepared Feature containing source text, screenshots, interview
answers, conflicting evidence, and each behavior classification, then inspect its export.

**Acceptance Scenarios**:

1. **Given** a requirement derived from a Business Rule and an interview answer, **When** its
   evidence is inspected, **Then** the relationship and original Spanish source are visible;
   relevant screenshot sources identify their Screen without fabricated quotations or links.
2. **Given** behavior described as current, desired, both, or undecided, **When** it is reviewed
   and exported, **Then** its classification is explicit in Spanish throughout; legacy behavior
   does not automatically become a desired requirement and an unknown intent stays undecided.
3. **Given** two sources conflict, **When** AI interprets them, **Then** it identifies both sources
   and the conflict, records a question or review marker, and does not choose a business rule
   without human clarification.
4. **Given** a readable screenshot that does not establish permissions or exceptions, **When** AI
   proposes requirements, **Then** it distinguishes visible evidence from unknown behavior and
   does not invent roles, permissions, workflows, integrations, exceptions, or legacy behavior.

### Edge Cases

- **EC-01 — Incomplete intake**: Partial Drafts can be saved. Analysis requires the intake
  described in FR-005; missing facts can be explicitly unknown rather than fabricated.
- **EC-02 — Unusable evidence**: Invalid files are rejected without losing saved work. A valid
  but blurry screenshot is retained as a source with limited usefulness; unreadable content is
  treated as missing evidence and cannot support an invented rule.
- **EC-03 — Interview interrupted or exhausted**: Reopening does not reset the seven-question
  limit. A resumed question retains its identity. Remaining gaps stay unresolved after the limit.
- **EC-04 — Contradictory sources or unclear intent**: Preserve both sources and make the conflict
  visible. Approval of supported content cannot settle an unanswered business decision implicitly.
- **EC-05 — Concurrent edits and approval changes**: If a Feature has changed since a participant
  began editing, their outdated save is blocked without overwriting the latest saved content.
  Preserve their unsaved edits while they review the latest version and reconcile their changes
  before retrying. A retry is checked again for intervening changes. Saved edits to an Approved
  Feature, including changes to source evidence, invalidate current approval. If content changes
  while someone reviews or exports, the action cannot accept a replacement, approve, or export
  different content from what was reviewed; the participant must review the current set.
- **EC-06 — AI or save failure**: Keep previously saved information and show a Spanish failure
  message with retry. Do not show unsaved work, failed generation, or failed approval as saved
  or complete; do not erase an existing reviewed set when regeneration fails. Successful
  regeneration also leaves the current requirements unchanged until replacement acceptance.
  Preparing or rejecting a replacement does not restore approval invalidated by source changes.
- **EC-07 — Empty requirement categories**: Keep all six export sections with an explicit empty
  state. Do not fill them with invented rules, tests, or assertions that no such behavior exists.
- **EC-08 — Multiple Screens with similar names**: Keep distinct source references under their
  owning Feature so evidence is not silently attributed to the wrong Screen or Feature.
- **EC-09 — Revoked project access**: Former participants cannot reopen, approve, or export
  application content after authorization is removed. Previously downloaded copies remain outside
  the application's control.
- **EC-10 — Real Project data in the wrong context**: A real screenshot or business detail from
  one Project cannot be displayed, analyzed for, or exported from another Project. Rejected or
  failed submissions do not create a visible Source for other Project participants.
- **EC-11 — Soft-deleted Feature**: A soft-deleted Feature is excluded from normal Project lists
  and cannot be viewed, edited, analyzed, approved, or exported through ordinary end-user actions.
  Its related Screens, sources, requirements, questions, and approvals remain associated with the
  deleted record for recovery rather than being permanently erased.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST organize Projects into Modules and Modules into Features. A Feature
  MUST be the discovery unit and group its Screens, Requirements, Sources, and Open Questions.
- **FR-002**: Initial intake MUST request only Module, Feature or Screen name, who uses it,
  screenshot, purpose, normal step-by-step workflow, and known rules, restrictions, or special
  cases. It MUST use plain Spanish without requiring formal requirements terminology.
- **FR-003**: Participants MUST be able to save and resume partial Drafts. Original Spanish input
  and interview answers MUST remain identifiable alongside AI interpretations and human corrections.
- **FR-004**: A Feature MUST support one or more labeled Screens and their uploaded screenshots.
  The POC MUST accept valid PNG and JPEG images up to 10 MB per file and reject unsupported,
  corrupt, or oversized files with Spanish guidance without losing previously saved content.
  A valid image with visually unclear content MAY be retained but MUST be marked as limited evidence.
  Authorized Project participants MUST be able to upload real SIFA screenshots for normal use;
  anonymization or test-data substitution MUST NOT be required by the application.
- **FR-005**: Before analysis, the Feature MUST have a selected Module, a name, at least one
  accepted screenshot, purpose and workflow descriptions, and a response for users and known
  rules. Users or rules MAY be explicitly unknown; rules MAY be reported as none known. Unknowns
  MUST become gaps for clarification or review, not invented answers.
- **FR-006**: Stakeholder controls, messages, interviews, summaries, reviewable requirements, and
  exports MUST be in Spanish. Generated or corrected wording MUST NOT overwrite original sources.
- **FR-007**: Each documented business behavior and behavior-bearing requirement MUST identify
  Current SIFA Behavior, Desired Behavior, Both, or Unknown / Needs Decision in Spanish. Inferred
  intent MUST remain undecided until supported by a source or an explicit human decision.
- **FR-008**: AI MUST base assertions on captured sources and MUST NOT invent business rules,
  roles, permissions, workflows, integrations, exceptions, or legacy behavior. Screenshot
  interpretation MUST NOT imply unseen behavior is established fact.
- **FR-009**: Material gaps, missing support, and source conflicts MUST remain visible as
  Feature-specific Open Questions or review markers tied to the affected content and known sources.
- **FR-010**: The interview MUST present at most seven distinct questions per Feature across
  sessions, retries, and edits. Presented but skipped or unanswered questions count toward the
  limit; a reload or failed attempt to obtain a new question does not add another question.
- **FR-011**: Questions MUST be presented one at a time with at most four selectable answers and
  optional free-text context. Closed questions MUST be preferred when they can resolve the gap;
  multi-select MUST be offered when supplied answers can apply together. Participants MUST be
  able to indicate they cannot answer without selecting an invented fact.
- **FR-012**: Questions MUST address material gaps in the current Feature and MUST NOT repeat
  already supplied information. Relevant areas include preconditions, roles and permissions,
  outcomes, exceptions, validations or errors, dependencies, and current versus desired behavior;
  these areas MUST NOT become a mandatory seven-question script.
- **FR-013**: The system MUST skip or end clarification when no material gap remains. At the
  question limit, any remaining uncertainty MUST stay unresolved and available for review.
- **FR-014**: Before generating requirements, the system MUST show a Spanish understanding
  summary covering purpose, users, workflow, rules, and uncertainties for human correction and
  confirmation. Corrections MUST be identified as human sources while preserving original input.
  Changed source information MUST require review and confirmation of an updated summary before
  new requirements are generated.
- **FR-015**: From confirmed understanding, the system MUST propose User Stories, Business Rules,
  Functional Requirements, Acceptance Criteria, Open Questions, and Sources in Needs Review.
  Categories without captured content MUST be explicit and MUST NOT be filled through invention.
  When requirements already exist, regeneration MUST produce a separate proposed replacement
  for review while preserving the current requirements and human edits. Any authorized Project
  participant MUST be able to accept or reject that replacement after reviewing it. The system
  MUST replace the current set only upon explicit acceptance; the replacement MUST enter Needs Review and require
  separate human approval before export. A pending replacement MUST NOT be exported as approved.
  If its sources or the current requirements change during generation or review, the replacement
  MUST be refreshed and reviewed against the latest content before acceptance.
- **FR-016**: Every authorized Project participant MUST be able to inspect, correct, or reject
  proposals in any Feature within that Project before approval. Human corrections MUST remain
  identifiable, and rejected assertions MUST be excluded from the approved requirements.
  Accepting a regenerated replacement MUST NOT erase original sources or identified human
  corrections; rejecting it MUST leave the current requirements and their human edits unchanged.
- **FR-017**: Requirements MUST reference supporting sources when available. Where Business Rules,
  Functional Requirements, and Acceptance Criteria derive from one another, those relationships
  MUST remain inspectable. Missing evidence MUST be flagged, never represented by fabricated links.
- **FR-018**: The lifecycle MUST distinguish Draft, Needs Review, and Approved. Intake begins in
  Draft, generation produces Needs Review, and only explicit human approval produces Approved;
  AI analysis, understanding confirmation, replacement acceptance, or export MUST NOT grant
  approval. Preparing a proposed replacement MUST NOT change the current set's state by itself;
  source changes MUST still invalidate approval as required by FR-020.
- **FR-019**: Approval MUST identify the human participant, time, and current approved content.
  Every identified user authorized for the Project MUST be able to approve any Feature in that
  Project, including Features created by other users. Remaining Open Questions MUST be acknowledged
  and remain unresolved; approval MUST NOT turn missing answers or unsupported dependent assertions
  into authoritative business requirements.
- **FR-020**: Changes to an Approved Feature's intake business content, screenshots, interview
  answers, understanding, or requirements MUST invalidate approval of the current set and return
  it to Needs Review. A participant MUST review and reconcile the changes before approving again.
  Approval or export MUST NOT apply silently to content changed since the participant reviewed it.
- **FR-021**: The system MUST restrict access to Project inputs, screenshots, proposals, approval
  actions, and exports to identified participants authorized for that Project. Authorization MUST
  be checked when those actions occur, including after a participant's access is removed.
  All authorized Project participants MUST have equal rights to view, edit, and approve every
  Feature in that Project, regardless of its creator; export remains restricted to Approved content.
  Real SIFA screenshots and business details MUST remain isolated to their owning Project and
  MUST NOT be exposed to other Projects or to unauthenticated users.
- **FR-022**: Final export MUST be available only for the current Approved requirements set and
  MUST reproduce its saved content without AI rewriting. Unresolved questions and review markers
  MUST remain labeled separately from approved assertions.
- **FR-023**: Export MUST produce a readable Spanish `.md` document with Feature, Module, Screen,
  and actor context; the six sections in FR-015; behavior classifications; approval details; and
  source identifiers, descriptions, and available relevant text. Screenshot references MUST
  identify the originating Screen without claiming the image is embedded when it is not.
- **FR-024**: The database MUST remain the source of truth for captured and approved information.
  Downloading or editing an exported Markdown file MUST NOT change the saved approved record.
- **FR-025**: Save, analysis, generation, approval, and export failures MUST provide Spanish
  feedback and retry without losing previously saved sources, answers, or reviewed requirements.
  Failed operations MUST NOT be represented as saved or complete or reset interview limits.
  If a Feature changed after a participant began editing, the system MUST reject their outdated
  save, preserve the latest saved content and their unsaved edits, and require reviewing the
  latest version and reconciling their edits before retrying. Each retry MUST check again for
  intervening changes; outdated content MUST NOT silently overwrite another participant's save.
- **FR-026**: The POC MUST exclude automatic Spec Kit execution, automatic GitHub publishing,
  screenshot annotations, automatic UI-element detection, autonomous agents, RAG or vector
  databases, fine-tuning, Superpowers integration, and automatic code generation from stakeholder
  input. The engineering handoff MUST remain a manual use of the exported Markdown document.
- **FR-027**: The POC MUST provide a complete, self-service end-user workflow for authorized
  Project participants: access their Project, create and resume Features, upload valid sources,
  run analysis and clarification, review and change proposed requirements, approve the current
  set, and export approved Markdown. No facilitator, administrator, or engineering user may be
  required to perform any of these end-user actions.
- **FR-028**: The POC MUST support production-like use with real SIFA business details and real
  screenshots. Authorized Project participants MUST be informed in Spanish before their first
  analysis that submitted Project sources are processed to produce AI-assisted proposals. The
  application MUST not represent test data or anonymization as a prerequisite for this workflow.
- **FR-029**: Authorized Project participants MUST be able to delete Features within their Project.
  Deletion MUST be a background soft deletion: it removes the Feature and all associated Screens,
  sources, requirements, Open Questions, and approvals from normal end-user lists and actions,
  while retaining the complete record for recovery. A soft-deleted Feature MUST NOT be available
  for ordinary viewing, editing, analysis, approval, or export.

### Key Entities *(include if feature involves data)*

- **Project**: The SIFA discovery context containing Modules and authorized participants who
  share equal viewing, editing, and approval rights across its Features.
- **Module**: A named business area within a Project that groups Features.
- **Feature**: The discovery unit with a name, users, purpose, workflow, rules, current state,
  Screens, Sources, Requirements, and Open Questions. It can be active or soft-deleted; a
  soft-deleted Feature is retained for recovery but excluded from normal end-user actions.
- **Screen**: A labeled legacy screen belonging to a Feature, associated with screenshot sources.
- **Source**: Original stakeholder wording, a screenshot, an interview answer, or an identified
  human correction or decision, with origin and stable identity for evidence references.
- **Interview Question and Answer**: A Feature-specific question, order within the seven-question
  allowance, offered choices, answer or skipped state, and optional contextual text.
- **Understanding Summary**: Reviewable interpretation of the Feature's sources, its uncertainties,
  and the participant's confirmation before requirements generation.
- **Requirement**: A User Story, Business Rule, Functional Requirement, or Acceptance Criterion,
  with Spanish content, behavior intent, supporting sources, and relationships to other items.
- **Proposed Replacement**: A regenerated requirements set kept distinct from the current set
  until explicit acceptance, with the sources and current content it was based on so outdated
  proposals can be detected. Acceptance changes the current set but does not grant approval.
- **Open Question**: An unresolved knowledge gap or business decision tied to affected content,
  with context and an answer only when a human or captured source supplies one.
- **Approval**: The identified human decision for the current requirements set, its time, and the
  content approved; later changes require a new decision.
- **Markdown Export**: A downloadable representation of approved content and explicitly unresolved
  questions, associated with the applicable approval rather than a separate source of truth.

## Success Criteria *(mandatory)*

### Measurable Outcomes

These are POC acceptance targets, not claims of measured performance. They verify a complete
self-service experience for authorized end users; evaluation thresholds are documented below.

- **SC-001**: Every end-to-end acceptance case can be completed by an authorized end user without
  a facilitator or staff member operating the workflow, from real Project intake through approved
  export, while preserving Project isolation.
- **SC-002**: At least four of five representative end users without formal requirements training
  complete a familiar single-Screen Feature within 20 minutes each,
  starting with a screenshot and knowledge of the workflow already available, including normal
  application waiting time and excluding voluntary breaks.
- **SC-003**: Every acceptance-test and end-user evaluation interview stays within seven questions per Feature,
  four selectable answers per question, and one question at a time; complete-input cases ask zero
  unnecessary questions and unanswered gaps remain visible.
- **SC-004**: Review of the acceptance-test and end-user outputs finds zero unsupported assertions
  represented as approved business facts. Every assertion has inspectable support or is separated
  as an unresolved item; every documented behavior carries an explicit behavior classification.
- **SC-005**: Every approval and export acceptance case prevents an unapproved or subsequently
  changed set from being represented as currently approved and identifies the approving human.
- **SC-006**: Every sampled approved export preserves the reviewed Spanish content and six required
  sections, includes Sources and unresolved questions, and can be opened as a readable Markdown
  document without access to internal application representations.
- **SC-007**: Two engineering reviewers can each identify the source, behavior intent, and approval
  status of five sampled requirements using the application and export, within two minutes per
  requirement and without asking the originating stakeholder to explain them again.
- **SC-008**: At least four of five representative end users rate the statement "I could document this
  Feature in my own words without knowing formal requirements terminology" at least four on a
  five-point agreement scale after completing their session.
- **SC-009**: Every deletion acceptance case removes the Feature and all related content from
  normal end-user lists and actions while retaining the complete record as a recoverable soft
  deletion; no ordinary end-user action can export or modify the deleted record.

## Assumptions

- **Scope interpretation**: The foundation supplied for the constitution is the feature brief
  for this single end-to-end POC. This engineering specification is in English; stakeholder-facing
  content and approved business requirements remain in Spanish. The POC is fully functional for
  authorized end users and is not limited to a facilitator-led pilot or demonstration.
- **Project setup dependency**: One SIFA Project and an agreed Module list are available before
  the pilot. Project and Module administration, shared Actor management, and a project Glossary
  are deferred; a Feature still records who uses its Screens in plain language.
- **Participant model (confirmed)**: The POC serves a known set of authorized SIFA participants.
  Every participant may view, edit, and approve any Feature in their Project and export its
  Approved requirements. There are no creator-only restrictions or separate Product Lead editing
  or approval privileges. Pilot membership must be established before use. Application
  participation is separate from legacy SIFA roles being documented.
- **Approval scope**: Approval applies to the complete current requirements set. Clearly labeled,
  acknowledged Open Questions may accompany approved supported content. Per-requirement approval
  workflows and browsing a full revision history are deferred; approval must still identify the
  exact content reviewed and detect subsequent changes. Reviewing a proposed replacement alongside
  the current set is included; accepting it and approving it are separate human decisions.
- **Real-data use (confirmed)**: The POC is a production-like end-user workflow. Authorized
  participants may upload real SIFA screenshots and business details; they do not need to replace
  them with test or anonymized data. Each participant is authorized to share the Project sources
  they submit. PNG and JPEG with a 10 MB per-file limit are initial POC choices, not limits
  supplied by the foundation. Markdown contains identifiable screenshot references; packaging
  image copies inside an export is deferred. Engineering reviewers need authorized application
  access to inspect those images.
- **Deletion behavior (confirmed)**: Authorized Project participants may delete Features, and
  deletion is a background soft delete. The Feature and its related data remain recoverable but
  are removed from normal end-user access. Retention duration, permanent purging, and the recovery
  operator or interface are not yet defined and require a separate data-retention decision before
  any irreversible purge capability is added.
- **AI dependency and fallback**: Analysis and generation depend on an available capability that
  can interpret Spanish text and screenshots. When unavailable, participants can retain and
  resume captured work; the POC does not require a manual replacement for requirements generation.
- **End-user evaluation**: Five representative end users, familiar single-Screen Features,
  screenshots, and two engineering reviewers will be available. The 20-minute completion target,
  two-minute evidence lookup target, and satisfaction thresholds are proposed POC defaults rather
  than measured baselines. Evaluation also includes prepared conflicting, incomplete, multi-Screen,
  and post-approval-change cases to verify boundaries not covered by a simple Feature. This
  evaluation validates a self-service workflow; it does not define a separate facilitator role.
- **Excluded capabilities**: The explicit POC exclusions in FR-026 and the constitution apply.
