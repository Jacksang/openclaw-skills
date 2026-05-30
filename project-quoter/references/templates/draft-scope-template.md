# Draft Scope Template

Copy this structure for any new project's `Draft scope.md`.

## Structure

```markdown
# Project {NAME} Platform - Feature Specification Catalog
**Version:** 1.0
**Date:** {DATE}
**Status:** Draft for Engineering Review
**Scope:** Phase 1–3+ Feature Definitions, Technical Constraints, and Acceptance Criteria

---

## 🟢 Phase 1: Core Platform (Must Have / Contracted)

### 1. {Feature Name}
- **Business Objective:** One sentence on why this exists
- **Functional Requirements:**
  - Bullet list of what the system must do
- **Non-Functional Requirements:** Performance, security, compliance
- **Technical Implementation Notes:** Stack choices, DB decisions, integration points
- **Acceptance Criteria:** How to verify it works
- **Dependencies & Risks:** What could block this

### 2. {Feature Name}
...

## 🟡 Phase 1: Core Platform (Good to Have / Conditional)
(Features desirable but not blocking MVP launch)

### N. {Feature Name}
...

## 🔵 Phase 2: Growth & Enhancement
(Features that scale and monetize — require Phase 1 revenue/profit gate)

### N. {Feature Name}
...

## 🟣 Phase 3+: Scale & Ecosystem
(Strategic vision — KPI-gated, requires Phase 2 validation)

### N. {Feature Name}
...

---

## 📝 Engineering Governance Notes
1. Scope Gating rules
2. Data Isolation requirements
3. CI/CD & Testing standards
4. Cost Control trade-offs
```

## Key Principles

- **One feature = one section.** No grouping dissimilar things.
- **Commitment levels are signals:** Green = contracted, Yellow = conditional, Purple = roadmap only.
- **Phase gates are explicit:** Phase 2 needs revenue; Phase 3 needs data + validation.
- **Non-functional requirements are not optional.** Write them even if the client doesn't ask.
- **Dependencies & risks** must be honest — this builds trust and prevents later disputes.
