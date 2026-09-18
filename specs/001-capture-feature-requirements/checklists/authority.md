# Authority & Resilience Requirements Checklist: SIFA Feature Discovery and Approved Requirements Export

**Purpose**: Review whether the requirements precisely define evidence authority, human approval, Project isolation, concurrency, recovery, and the bounded POC scope before implementation.
**Created**: 2026-09-18
**Feature**: [spec.md](../spec.md)
**Depth**: Standard reviewer checklist
**Focus**: Evidence/approval authority; access, concurrent changes, and recovery

**Note**: This custom checklist evaluates requirements quality. It does not test implementation behavior.
**Review Ownership**: This checklist is reviewer-owned. Mark an item `[x]` only when the reviewer determines the requirements-quality criterion is satisfied.
**Marker Semantics**: `[x]` means the criterion has been reviewed and satisfied for requirements quality. It does not mean implementation work is complete.

## Evidence and Authority Requirements

- [ ] CHK001 Are the conditions that make a source eligible to support an approved assertion defined separately from the conditions that merely retain it as limited or unreadable evidence? [Clarity, Spec `FR-004, FR-008–009]
- [ ] CHK002 Do the requirements distinguish an AI interpretation, a stakeholder source, and an identified human correction or decision throughout capture, review, and export? [Consistency, Spec `FR-003, FR-006, FR-014–017]
- [ ] CHK003 Is “supporting source when available” precise about the treatment of a claim with no available source, insufficient evidence, or conflicting sources? [Completeness, Spec `FR-009, FR-017]
- [ ] CHK004 Are the conditions under which a screenshot can support visible facts versus remain insufficient for permissions, exceptions, workflows, or integrations stated without ambiguity? [Clarity, Spec `FR-008; User Story 5.4]
- [ ] CHK005 Is the requirement for preserving original Spanish wording clear enough to distinguish semantic preservation from presentation escaping or formatting in the Markdown export? [Clarity, Spec `FR-003, FR-006, FR-022–023]
- [ ] CHK006 Are “Current SIFA Behavior,” “Desired Behavior,” “Both,” and “Unknown / Needs Decision” defined so reviewers can classify conflicting or partially supported evidence consistently? [Clarity, Spec `FR-007; User Story 5.2]
- [ ] CHK007 Do requirements prevent an acknowledged Open Question from being interpreted as approval of an unsupported dependent assertion? [Consistency, Spec `FR-009, FR-019, FR-022]
- [ ] CHK008 Are requirement-to-source and requirement-to-requirement traceability expectations defined for cases where a Business Rule is rejected or loses support after a downstream requirement was drafted? [Coverage, Spec `FR-016–017, EC-04]
- [ ] CHK009 Does the specification state what a reviewer must be able to inspect to determine why each approved assertion exists, including screenshots with limited readability? [Measurability, Spec `FR-017, SC-004, SC-007]
- [ ] CHK010 Are the requirements for explicit empty requirement categories clear about the difference between “no captured content” and “no such business behavior exists”? [Clarity, Spec `FR-015, EC-07]

## Lifecycle, Approval, and Export Requirements

- [ ] CHK011 Are Draft, Needs Review, and Approved definitions complete for first generation, human edits, regeneration, replacement acceptance, rejection, and soft deletion? [Completeness, Spec `FR-015, FR-018–020, FR-029]
- [ ] CHK012 Is it unambiguous which distinct human decisions are required for understanding confirmation, proposed-replacement acceptance, reconciliation after changes, and approval? [Clarity, Spec `FR-014–015, FR-018–020]
- [ ] CHK013 Do approval requirements identify the complete reviewed content boundary, including Feature/Module/Screen context, sources, behavior intent, unresolved questions, and approval identity/time? [Completeness, Spec `FR-019, FR-022–024, SC-005–006]
- [ ] CHK014 Are the freshness rules for approval and export consistent when content changes between review, approval, and download? [Consistency, Spec `FR-020, FR-022, FR-025, EC-05]
- [ ] CHK015 Is the phrase “current Approved requirements set” defined precisely enough to exclude pending replacements, rejected assertions, and older approvals from export? [Clarity, Spec `FR-015–016, FR-022]
- [ ] CHK016 Are regeneration requirements complete for a failed proposal, a rejected proposal, and an accepted proposal when the current set contains human edits? [Scenario Coverage, Spec `FR-015–016, EC-06]
- [ ] CHK017 Does the specification make clear which changes invalidate approval, including intake values, Screens, interview answers, understanding, requirements, and unresolved-question decisions? [Completeness, Spec `FR-020, EC-05]
- [ ] CHK018 Are approved-export requirements specific enough to prevent AI paraphrase, hidden source loss, non-deterministic document content, or the implication that screenshots are embedded? [Clarity, Spec `FR-022–024; User Story 3]
- [ ] CHK019 Can the success criteria objectively distinguish a readable Spanish Markdown export from one that faithfully represents the exact approved record? [Measurability, Spec `SC-005–006]
- [ ] CHK020 Are the manual GitHub Spec Kit handoff boundary and every excluded automation named consistently across requirements, assumptions, and success criteria? [Consistency, Spec `FR-026; Assumptions “Excluded capabilities”]

## Access, Concurrency, and Recovery Requirements

- [ ] CHK021 Do Project-access requirements define equal participant rights separately from access to legacy roles described by the Feature? [Clarity, Spec `FR-021; Assumptions “Participant model”]
- [ ] CHK022 Are authorization requirements complete for every protected artifact and action: intake, Screens, uploaded sources, AI proposals, approvals, exports, and recoverable deleted records? [Completeness, Spec `FR-021, FR-029, EC-09–011]
- [ ] CHK023 Is “authorization checked when actions occur” specific about retries and operations that began before membership revocation or deletion? [Clarity, Spec `FR-021, FR-025, EC-09, EC-11]
- [ ] CHK024 Do requirements explicitly address the risk of joining otherwise valid records from different Projects when a participant belongs to both? [Gap, Spec `FR-001, FR-017, FR-021, EC-10]
- [ ] CHK025 Are conflict requirements clear on which user edits must be preserved, what current content must be shown, and what explicit reconciliation is required before a retry? [Clarity, Spec `FR-025; User Story 1.6; EC-05]
- [ ] CHK026 Do concurrency requirements cover stale results for all authority-changing paths, including a generated replacement, acceptance, approval, and export? [Coverage, Spec `FR-015, FR-020, FR-022, FR-025, EC-05]
- [ ] CHK027 Is the seven-question ceiling defined consistently for a failed request, reload, skipped answer, newly resolved outstanding gap, and concurrent requests for the next question? [Consistency, Spec `FR-010–013, EC-03, EC-06]
- [ ] CHK028 Are failure/retry requirements complete for invalid/corrupt uploads, storage or AI unavailability, incomplete analysis, failed approval, and failed download without misrepresenting unsaved or unapproved content as complete? [Scenario Coverage, Spec `FR-004, FR-025, EC-02, EC-06]
- [ ] CHK029 Is soft deletion sufficiently specified for immediate ordinary-access removal, retained related records, in-flight operations, recovery authority, and future retention/purge decisions? [Completeness, Spec `FR-029, EC-11; Assumptions “Deletion behavior”]
- [ ] CHK030 Are the self-service and real-data requirements explicit enough to assess whether setup dependencies, notices, and recovery limitations accidentally reintroduce a facilitator or anonymization prerequisite? [Consistency, Spec `FR-027–028, SC-001; Assumptions “Project setup dependency”]

## Measurability and Scope Boundaries

- [ ] CHK031 Are the five-user and two-reviewer evaluation populations, timing rules, sample selection, and scoring criteria sufficiently defined to measure SC-002, SC-007, and SC-008 consistently? [Clarity, Spec `SC-002, SC-007–008; Assumptions “End-user evaluation”]
- [ ] CHK032 Is SC-004’s “zero unsupported assertions” accompanied by a consistent review method that distinguishes missing evidence, conflicting evidence, and valid unresolved questions? [Measurability, Spec `SC-004, FR-008–009, FR-019]
- [ ] CHK033 Do non-functional requirements define the acceptable latency, timeout, or degraded-user-feedback expectations for AI-assisted steps, or is their omission intentional for this POC? [Gap, Spec `FR-025, SC-002; Assumptions “AI dependency and fallback”]
- [ ] CHK034 Are privacy/data-handling requirements for real screenshots and business details sufficiently explicit about notice, access, retention, and any external AI-processing constraints, or does this require a separate governance decision? [Gap, Spec `FR-021, FR-028; Assumptions “Real-data use”]
- [ ] CHK035 Does the specification clearly distinguish deferred Project/Module administration, recovery/purge, history browsing, and image packaging from the required end-user workflow so implementation scope cannot expand implicitly? [Completeness, Spec `FR-026–029; Assumptions]
- [ ] CHK036 Are constitution principles I–V traceable to concrete functional requirements and measurable acceptance evidence without relying only on the implementation plan? [Traceability, Spec `FR-006–009, FR-015–026, SC-001–009]

## Notes

- Leave items unchecked until a reviewer evaluates the written requirements and records any clarification or correction.
- This checklist was created separately from `requirements.md`, which is the built-in specification-quality checklist maintained by SpecKit.
- `$speckit-implement` may read checklist state as a gate but must not change these markers.
