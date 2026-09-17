# Project Initialization Foundation

## 1. Project Purpose

### What are we building?

sifa-pecs-creator is a web-based tool that will help to take new requirements from stakeholders and convert them into a functional RDP for the product lead, without the need of having extensive meetings all the time any stakeholder have a new idea. This will turn the discovery sessions async and just use the meeting to refine and approve those requirements.

The application structure will be divided by Projects, Modules, Features, Screens, Requirements, Business Rules, Specs.

### Who is it for?

- Stakeholders of projects with a lot of business logic knowledge and the know how of the product.
- Engineers and architects working in the project.

### What problem does it solve?

Sometimes the stakeholders don't have all the concrete ideas of the feature we are building right away, or they are too busy during the week to jump into a 1-2 hours meetings.
This is going to reduce the friction about what they wat to build in the product and improve the communication between technical and non-technical team members.

---

## 2. Technical Constraints

- **Frontend:** React, Tailwind CSS
- **Backend:** Node.js with Express
- **Database:** Supabase PostgreSQL
- **Language:** TypeScript
- **Hosting / Infrastructure:** Vercel
- **Important Libraries:** Shadcn

---

## 3. Domain Rules

Document the rules that the system must enforce regardless of implementation.

Examples:

- A reservation cannot overlap another reservation for the same resource.
- A user must be authenticated before creating a reservation.
- Only authorized roles may modify certain records.

These rules are authoritative while they remain part of the project's approved documentation.

-

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