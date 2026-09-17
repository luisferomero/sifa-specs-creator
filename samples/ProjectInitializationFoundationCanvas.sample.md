# Project Initialization Framework (PIF) Canvas

**Project Name:** Atlas Cloud POS
**Project Type:** Legacy System Modernization
**Document Version:** 0.1
**Status:** Draft
**Project Owner:** Product / Business Stakeholder
**Technical Owner:** Engineering Lead

---

# 1. Project Context

## 1.1 What Are We Building?

Atlas Cloud POS is a web-based point-of-sale and business management platform intended to replace an existing desktop application that has been in use for several years.

The current system contains significant business logic related to sales, inventory, purchasing, customers, reporting, pricing, taxes, and operational workflows.

The new platform will modernize the user experience and technical architecture while preserving validated business behavior.

The initial product will be delivered incrementally rather than attempting to reproduce the entire legacy application at once.

---

## 1.2 Why Does This Project Exist?

The existing application has become increasingly difficult to maintain, extend, deploy, and integrate with modern services.

The project exists to:

- Reduce dependency on legacy technologies.
- Improve maintainability.
- Enable cloud deployment.
- Improve accessibility across devices and locations.
- Establish a foundation for future integrations.
- Reduce operational risk caused by undocumented legacy behavior.
- Improve the development team's ability to deliver new functionality.

The goal is not simply to rewrite the existing application.

The goal is to create a maintainable modern product while preserving the business knowledge embedded in the current system.

---

## 1.3 Who Are the Primary Users?

Primary users include:

- Store employees
- Store managers
- Administrative staff
- Purchasing personnel
- Accounting or finance personnel
- Business owners
- Internal support staff

Different roles may have different permissions and access to functionality.

---

## 1.4 Current State

The existing application:

- Runs primarily as a desktop application.
- Contains years of accumulated business logic.
- Has limited automated testing.
- Has incomplete technical documentation.
- Relies heavily on stakeholder knowledge.
- Contains workflows that are understood operationally but may not be formally documented.
- Has tightly coupled user interface, data access, and business logic in several areas.

Because of this, existing source code alone must not be treated as the definitive description of intended business behavior.

Stakeholder validation is required.

---

## 1.5 Desired Future State

The future platform should:

- Run as a modern web application.
- Be deployable to cloud infrastructure.
- Have clearly separated application layers.
- Have documented business rules.
- Have automated testing around critical logic.
- Support incremental development.
- Be observable and diagnosable in production.
- Allow future teams and AI-assisted development tools to understand the architecture and project rules.

---

# 2. Project Success Definition

The project will be considered successful when the organization can progressively transition from the legacy application to the modern platform without losing critical business functionality.

Success is measured primarily by:

1. Business-rule correctness.
2. Data integrity.
3. System reliability.
4. Maintainability.
5. Usability.
6. Delivery velocity.

Development speed is valuable but must not come at the expense of business correctness or data integrity.

---

# 3. Success Priorities

These priorities establish the project's general decision-making order.

## 3.1 Business Correctness

The new system should accurately implement validated business rules.

Existing behavior should not automatically be considered correct simply because the legacy application currently behaves that way.

When behavior is unclear, the responsible business stakeholder should validate the intended rule.

---

## 3.2 Data Integrity

The system must protect the accuracy and consistency of transactional and operational data.

Operations affecting financial, inventory, purchasing, or customer records should avoid partial or inconsistent updates.

---

## 3.3 Maintainability

Code should prioritize readability, predictable structure, and understandable behavior.

Developers should be able to understand the purpose of a component without having to reconstruct the entire system mentally.

---

## 3.4 Reliability

Critical workflows should fail predictably and provide sufficient information for diagnosis.

Silent failures should be avoided.

---

## 3.5 User Experience

The new application should improve the usability of legacy workflows rather than reproduce outdated interfaces exactly.

The business workflow should be preserved when required, but the presentation may be redesigned.

---

## 3.6 Development Velocity

The team should favor approaches that allow frequent delivery and incremental validation.

Large rewrites without intermediate stakeholder validation should be avoided.

---

# 4. Non-Negotiables

These are constraints expected to apply throughout the project.

## 4.1 Business Rules

Critical business behavior must be documented before or during implementation.

A developer must not intentionally change an existing validated business rule without an approved product decision.

When legacy behavior conflicts with stakeholder-defined intended behavior, the stakeholder-approved rule becomes authoritative.

---

## 4.2 Data

Production data must not be modified through undocumented or unreviewed processes.

Schema changes should be traceable and reproducible.

Critical database migrations must include a rollback or recovery strategy when practical.

---

## 4.3 Security

Authentication and authorization must be enforced by the application rather than relying exclusively on user-interface restrictions.

Sensitive configuration values must not be committed to source control.

Production credentials and secrets must use an approved secret-management mechanism.

---

## 4.4 Architecture

Core business rules must not depend directly on user-interface components.

External integrations should be isolated behind clearly defined boundaries.

Infrastructure-specific concerns should not be embedded throughout domain or business logic.

---

## 4.5 Documentation

Important business rules discovered during implementation must be captured in project documentation.

Knowledge that affects system behavior should not remain solely in chat conversations, meetings, or individual developer knowledge.

---

# 5. Engineering Philosophy

## 5.1 Simplicity Before Abstraction

The project should prefer the simplest architecture that satisfies current validated requirements.

Abstractions should solve demonstrated problems rather than hypothetical future needs.

---

## 5.2 Explicit Over Implicit

Important system behavior should be understandable from the code, configuration, or documentation.

Hidden conventions and unexpected side effects should be minimized.

---

## 5.3 Business Logic Is a First-Class Concern

Business rules should be identifiable and testable independently from presentation concerns whenever practical.

Complex rules should not be buried inside controllers, UI components, database triggers, or integration code without strong justification.

---

## 5.4 Modular Boundaries

Major business capabilities should have clear boundaries.

Examples may include:

- Sales
- Inventory
- Purchasing
- Customers
- Vendors
- Accounting
- Reporting

Modules may interact, but ownership of rules and responsibilities should remain clear.

---

## 5.5 Prefer Proven Technology

New dependencies, frameworks, and architectural patterns should provide clear value.

The project should not adopt technology primarily because it is new or fashionable.

---

## 5.6 Incremental Delivery

Large capabilities should be divided into independently understandable and testable increments whenever possible.

Stakeholders should be able to validate behavior throughout development rather than only at the end.

---

# 6. Quality Standards

## 6.1 Definition of Done

A feature is not considered complete solely because the code has been written.

Depending on the feature, completion may require:

- Functional requirements implemented.
- Acceptance criteria satisfied.
- Relevant automated tests passing.
- Business rules validated.
- Appropriate error handling.
- Logging or observability added.
- Required documentation updated.
- Code reviewed.
- Deployment successfully completed in the appropriate environment.

---

## 6.2 Testing

Critical business logic should have automated tests.

Defects involving important business rules should generally result in a regression test when practical.

Testing effort should be proportional to the risk of the functionality.

High-risk functionality includes areas such as:

- Financial calculations
- Inventory adjustments
- Payments
- Taxes
- Permissions
- Data migrations
- External synchronization

---

## 6.3 Code Review

Changes affecting production code should normally be reviewed before merging.

Reviews should evaluate more than syntax.

Reviewers should consider:

- Correctness
- Business-rule compliance
- Maintainability
- Security
- Architecture
- Tests
- Scope

---

## 6.4 Observability

Production behavior should be diagnosable.

Important failures should generate sufficient contextual information to understand what occurred without requiring reproduction whenever practical.

---

# 7. Architecture Guardrails

These guardrails define boundaries rather than implementation details.

## 7.1 Separation of Concerns

Presentation, application logic, business rules, persistence, and external integrations should have clearly understandable responsibilities.

---

## 7.2 Domain Independence

Core business logic should not require knowledge of the web framework, database provider, or user-interface library unless technically unavoidable.

---

## 7.3 External Integrations

External systems should be treated as unreliable dependencies.

Integration code should account for scenarios such as:

- Timeouts
- Unavailable services
- Invalid responses
- Rate limits
- Partial failures

Integration-specific behavior should remain isolated from unrelated business logic.

---

## 7.4 Database Access

Database access should follow consistent patterns.

Business rules should not be scattered across unrelated queries, stored procedures, application services, and UI components without clear ownership.

---

## 7.5 API Design

Public or internal APIs should use predictable contracts.

Breaking API changes should be intentional and documented.

---

# 8. Product and Engineering Decision Rules

These rules help resolve trade-offs.

## 8.1 Correctness vs Speed

When delivery speed conflicts with verified business correctness, correctness takes priority.

---

## 8.2 Data Integrity vs Convenience

Data integrity takes priority over implementation convenience.

---

## 8.3 Simplicity vs Future Flexibility

Prefer the simpler implementation unless there is a validated requirement for additional flexibility.

---

## 8.4 Existing Behavior vs Intended Behavior

Legacy behavior provides evidence of how the system currently works.

It does not automatically define how the new system must work.

Validated business intent takes priority.

---

## 8.5 Consistency vs Local Optimization

Prefer consistency with established project patterns unless deviating provides a meaningful and documented benefit.

---

## 8.6 Build vs Buy

External services may be used when they significantly reduce complexity and do not introduce unacceptable cost, security, reliability, or vendor-dependency risks.

---

# 9. Requirements Discovery Rules

Features should begin with the business problem rather than implementation details.

Requirements should identify:

- User or actor.
- Desired outcome.
- Relevant workflow.
- Business rules.
- Validation requirements.
- Permissions.
- Important states.
- Failure scenarios.
- Acceptance criteria.

Implementation details should normally be deferred until planning.

---

# 10. Legacy Migration Rules

Because this project replaces an existing system, additional rules apply.

## 10.1 Legacy Code Is Evidence, Not Specification

Existing code may be examined to understand behavior but must not automatically be treated as the definitive business specification.

---

## 10.2 Stakeholder Knowledge Must Be Captured

When stakeholders explain undocumented behavior, important rules should be added to project documentation.

---

## 10.3 Migration Should Be Incremental

Modules should be migrated in manageable increments whenever possible.

Each migrated area should be validated before major downstream dependencies are built on top of it.

---

## 10.4 Unknown Behavior Must Be Explicit

When a business rule cannot be confidently determined, it should be marked as unresolved rather than guessed.

---

# 11. AI-Assisted Development Rules

AI-assisted tools may be used throughout analysis, planning, coding, testing, and documentation.

AI-generated output must still comply with project engineering standards.

AI tools should not independently invent:

- Business rules.
- Product requirements.
- Security requirements.
- Permission models.
- Financial logic.
- Architectural exceptions.

When required information is missing, uncertainty should be surfaced explicitly.

Project documentation should be treated as the primary context supplied to AI agents.

---

# 12. Governance

## 12.1 Ownership

Product decisions are owned by the designated product or business authority.

Technical architecture decisions are owned by the designated engineering authority.

Decisions affecting both areas should be discussed jointly.

---

## 12.2 Constitution Changes

Project principles may evolve as the team learns more about the system.

Changes to foundational rules should be intentional and documented.

---

## 12.3 Exceptions

Temporary exceptions to project principles may be allowed when justified.

Significant exceptions should document:

- What rule is being bypassed.
- Why the exception is necessary.
- Risks introduced.
- Whether corrective work is required later.

---

## 12.4 Versioning

Foundational project principles should be versioned.

Suggested approach:

- **Major:** removal or fundamental change of an existing principle.
- **Minor:** addition of a new principle or significant expansion.
- **Patch:** clarification without changing expected behavior.

---

# 13. Open Questions

The following items remain unresolved during initial project setup:

- Final authentication provider.
- Final cloud hosting architecture.
- Detailed permission model.
- Legacy data migration strategy.
- Reporting architecture.
- Offline requirements.
- Disaster-recovery targets.

These questions should not be converted into assumptions.

They should be resolved during discovery or feature planning.

---

# 14. Constitution Candidates

The following PIF decisions appear strong enough to become project-wide constitutional principles.

### Candidate I — Business Rule Integrity

Validated business behavior must not be modified without an explicit product decision.

### Candidate II — Data Integrity

Operations must preserve consistent business data and avoid partial state wherever reasonably possible.

### Candidate III — Business Logic Independence

Core business rules must remain clearly separated from UI and infrastructure concerns.

### Candidate IV — Simplicity First

The simplest solution satisfying validated current requirements should be preferred over speculative flexibility.

### Candidate V — Test Critical Behavior

Critical business rules must be protected by automated tests where technically practical.

### Candidate VI — Explicit Uncertainty

Unknown requirements or business behavior must be identified rather than guessed.

### Candidate VII — Incremental Delivery

Significant functionality should be delivered and validated incrementally whenever practical.

### Candidate VIII — Documentation as Project Memory

Important business and architectural decisions must be captured in persistent project documentation.

---

# 15. Documents Produced From This Canvas

The PIF Canvas should serve as source material for the project's foundational documentation.

Expected outputs may include:

```text
PROJECT-FOUNDATION.md
        │
        ├── PRODUCT.md
        │
        ├── constitution.md
        │
        ├── ARCHITECTURE.md
        │
        ├── DESIGN.md
        │
        └── AGENTS.md
                │
                ▼
           Feature Specs
                │
                ▼
              Plans
                │
                ▼
              Tasks
```

Not every statement in this document belongs in the Constitution.

The Constitution should contain only project-wide principles, non-negotiable constraints, quality gates, architectural guardrails, and governance rules that should apply consistently across future work.