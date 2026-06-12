# Module Plan Template

Each module gets its own file: `plan/E0X-module-name.md`. Module IDs reuse the planner's epic IDs.

**Note on user stories and behavior tests:** if `project-planner` artifacts exist (`plan/E0X-[role]-stories.md`, `tests/E0X-[role]-tests.md`), reference them by ID in sections 1 and 3 instead of rewriting them. Only write them inline for quote-only engagements where the planner was not run.

## Structure

```markdown
# E0X: Module Name
**Module ID:** E0X
**Version:** 1.0
**Date:** {DATE}
**Phase:** 1 | 2 | 3 — Phase Description
**Owner:** Project Engineering
**Dependencies:** E07 (Infrastructure), E01 (Auth), etc.

---

## 1. User Stories

### Persona: {Role Name}
| # | User Story | Acceptance Criteria |
|---|-----------|---------------------|
| US-01 | As a **{Role}**, I want to {action}, so {benefit}. | AC-01: {criterion}. AC-02: ... AC-03: ... AC-04: ... AC-05: ... |

(Repeat for each persona — typically 3–5 personas per module)

Minimum personas to cover:
- End User (Customer/Member)
- Operator (Staff/Therapist/Admin)
- System (security, compliance, data integrity)

---

## 2. Technical Tasks

### Task T1: {Task Name}
**Description:** What this task builds.

**Input Schema:**
```json
{ "POST /api/v1/endpoint": { "field": "type (constraint)" } }
```

**Output Schema:**
```json
{ "200": { ... }, "40x": { ... } }
```

| # | Unit Test Case | Input | Expected |
|---|---------------|-------|----------|
| 1 | {Test scenario} | {Input data} | {Expected output/status} |
| 2 | ... | ... | ... |
(Minimum 5 test cases per task, aim for 8)

(Repeat for each task — typically 4–8 tasks per module)

---

## 3. Test Documentation — User Behavior Tests

| ID | Scenario | Steps | Expected Result |
|----|----------|-------|-----------------|
| TC-01 | Happy Path: {scenario} | 1. Step 2. Step 3. Step | Expected outcome |
| TC-02 | Edge: {scenario} | ... | ... |
| TC-03 | Error: {scenario} | ... | ... |
| TC-04 | Security: {scenario} | ... | ... |

Minimum 10 behavior test scenarios per module. Cover:
- Happy path (2–3)
- Edge cases (3–4)
- Error handling (2–3)
- Security/authorization (2–3)

---

## 4. Effort Estimates

| Task | Description | Size | Work (h) | AI wall-clock (h) |
|------|-------------|------|----------|-------------------|
| T1 | {Name} | XS/S/M/L/XL/XXL | XX | XX |

Human hours are **not** per-task — roll up gate-based hours at project level in `MASTER_SUMMARY.md` (see dual-track-estimation.md).

**Frontend tasks:** For each PAGE in `plan/UI_IMPLEMENTATION_MANIFEST.md`, include a module task row (or reference `C*-UI-*` SCRUM IDs) — API-only module plans cause the UI omission gap.
| T2 | ... | ... | ... | ... |
| — | Integration testing & end-to-end | — | X | X |
| — | **TOTAL** | — | **XXX** | **XXX** |

**Calendar Estimate:** ~X working days (N devs × X days + 1 QA × Y days, with Z days buffer).
**Critical Path:** {Which tasks must complete first}
**Risk Buffer:** +X days for {specific risk}
```

## Estimation Rules

Use the formulas in `project-quoter/SKILL.md` Stage 3 — they are the single source of truth:

- **Task sizing:** t-shirt sizes XS (1–8h) / S (8–20h) / M (20–40h) / L (40–80h) / XL (80–160h) / XXL (160–400h)
- **Test hours:** dev_hours × qa_ratio (0.3 simple / 0.4 medium / 0.5 complex, +0.1 for heavy integration surface)
- **Calendar days:** (dev + qa) / 6h daily capacity × team_size_factor (1.0 / 0.6 / 0.45 for 1/2/3 devs) × risk_multiplier (1.15 / 1.25 / 1.4)
