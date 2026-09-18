# Approved Markdown Export Contract

**Format**: UTF-8 Markdown (.md), Spanish, generated deterministically from the database. No provider call, GitHub write, or Spec Kit invocation.

## Authorization and snapshot

The request identifies the approval/set/edit/source revisions the participant viewed. In one transaction verify live membership, active Feature, effective APPROVED state, current approval ID, exact revisions, and retained reviewed content. Lock membership/Feature consistently with mutation commands, materialize all snapshot fields, and then release locks. Render only that immutable projection.

A concurrent change committed before snapshot acquisition returns CONFLICT; do not select a newer approval silently. A later change invalidates future exports but does not rewrite bytes already authorized/materialized. Neither pending proposals nor rejected assertions appear as approved content.

Failure returns a Spanish error without partial Markdown or mutation. Retrying the same current approval produces the same bytes. Do not add a varying download timestamp; approval time belongs in the fixed document.

## Required order and content

| Section | Required data |
|---|---|
| Document title and approval metadata | Feature title, Project/Module, stable Feature ID, approval ID, set revision, approver display identity and UTC approval time |
| Contexto | Approved purpose, workflow, who uses it, known rules/unknowns, Screens with stable IDs and approved labels |
| Historias de usuario | Included supported USER_STORY rows, stable identifiers, Spanish wording, intent, source references |
| Reglas de negocio | Included supported BUSINESS_RULE rows and derivation/support links |
| Requisitos funcionales | Included supported FUNCTIONAL_REQUIREMENT rows and linked Business Rules |
| Criterios de aceptación | Included supported ACCEPTANCE_CRITERIA rows and related requirements |
| Preguntas abiertas | Unresolved gaps, conflicts, unknown decisions, excluded uncertain dependent assertions/review markers, acknowledgement; never silently answered |
| Fuentes | Stable source IDs, origin type, human author where applicable, available original Spanish text, screenshot/Screen references and limitations |

All six required categories remain present even when empty. Use "No se ha capturado contenido para esta sección." for an empty requirement category; it does not assert that no such behavior exists. If there are no open gaps use "No hay preguntas abiertas registradas en esta revisión." Original wording is preserved semantically exactly; escaping Markdown syntax must not rewrite business meaning.

For each documented behavior render the appropriate label: Comportamiento actual de SIFA; Comportamiento deseado; Ambos; Desconocido / Requiere decisión. UNKNOWN intent is never presented as a confirmed desired requirement. If an uncertainty affects factual support, the dependent assertion stays in the uncertainty section and outside approved assertions.

## Evidence references and serialization

Use deterministic IDs derived from stored stable identifiers, ordering by category/position/stable ID. Retain rule -> functional requirement -> acceptance criterion relationships and source links. Missing text remains missing, with explicit limitation; do not invent quotations or captions.

A screenshot reference contains Source ID, Screen ID/label, original filename, and a stable authenticated application source link at /projects/{projectId}/features/{featureId}?source={sourceId}. The Feature view opens that evidence panel only after current authorization. It says "Imagen disponible en la aplicación para participantes autorizados; no incluida en este archivo." Do not embed image bytes, private object URLs, signed tokens, or claim an image package exists. Source references remain identifiable if the reader lacks application access.

Escape untrusted HTML and Markdown control syntax, normalize line endings to LF, retain accents, and serialize UTC times consistently. Use a safe sanitized filename with a stable Feature suffix and .md extension; preserve the original title inside the document.

Response headers: Content-Type text/markdown; charset=utf-8; Content-Disposition attachment with safe filename; Cache-Control private, no-store. No browser or shared export cache may bypass authorization.

## Contract verification

A snapshot fixture with all requirement types, all intent values, human corrections, a rejected item, unsupported dependent content, Screens, and an acknowledged unresolved question must render identically on repeated calls. Compare every rendered authoritative assertion against the approved relational projection. Check Spanish accents and escaping, all six headings, evidence/relationship IDs, empty categories, and absence of pending/rejected/unapproved material.

Verify stale approval/token, unauthorized user, membership revoked with an existing session, and deleted Feature return no document. Simulate an edit racing snapshot acquisition and prove the result is either the original consistent approved projection or a conflict, never mixed revisions. Editing a downloaded file has no effect on database content.
