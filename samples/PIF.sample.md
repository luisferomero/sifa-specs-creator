# Project Initialization Foundation

## 1. Project Purpose

### What are we building?
Describe the application in a few sentences.

### Who is it for?
Identify the primary users.

### What problem does it solve?
Explain the main business need.

---

## 2. Technical Constraints

Define technology decisions that are already established.

- **Frontend:**
- **Backend:**
- **Database:**
- **Language:**
- **Hosting / Infrastructure:**
- **Important Libraries:**

Avoid adding technologies that have not actually been decided.

---

## 3. Domain Rules

Document the rules that the system must enforce regardless of implementation.

Examples:

- A reservation cannot overlap another reservation for the same resource.
- A user must be authenticated before creating a reservation.
- Only authorized roles may modify certain records.

These rules are authoritative while they remain part of the project's approved documentation.

If a rule changes, the relevant specification or project documentation must be updated before the implementation is changed.

---

## 4. Engineering Rules

Define how the project should be implemented.

### Architecture
- Preferred project structure.
- Architectural patterns to use.
- Patterns explicitly avoided.
- Module boundaries when relevant.

### Code Style
- Naming conventions.
- Functional vs object-oriented preferences.
- Reusability expectations.
- Dependency rules.

### Simplicity
Do not introduce abstractions, infrastructure, dependencies, or features without a current requirement that justifies them.

---

## 5. Quality & Safety Rules

### Validation
Define important validation expectations.

### Error Handling
Define how technical failures should be handled.

### Security
Define authentication, authorization, secrets, and sensitive-data expectations.

### Testing
Identify which functionality requires automated tests.

---

## 6. AI / SDD Rules

### Specification First

Implementation must follow the approved specification.

Do not introduce functionality that is not required by the specification.

### No Speculation

Do not invent:

- Business rules
- Features
- User roles
- Permissions
- Integrations
- Data requirements

when they have not been established.

### Conflict Handling

If a specification conflicts with the Project Constitution, identify the conflict before implementation.

If the requested behavior represents an intentional change to a foundational rule, update the appropriate documentation first.

### Scope Discipline

Do not implement features “for later,” speculative abstractions, or unnecessary infrastructure.

Build only what the current specification requires.