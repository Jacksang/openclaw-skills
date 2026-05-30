# Module Plan Template

Each module gets its own file: `plan/E0X-module-name.md`

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

| Task | Description | Complexity | Est. Dev (hrs) | Est. Test (hrs) |
|------|-------------|------------|-----------------|-----------------|
| T1 | {Name} | Low/Med/High | XX | XX |
| T2 | ... | ... | ... | ... |
| — | Integration testing & end-to-end | — | X | X |
| — | **TOTAL** | — | **XXX** | **XXX** |

**Calendar Estimate:** ~X working days (N devs × X days + 1 QA × Y days, with Z days buffer).
**Critical Path:** {Which tasks must complete first}
**Risk Buffer:** +X days for {specific risk}
```

## Estimation Rules

### Complexity Classification
| Level | Criteria | Typical Range |
|-------|----------|---------------|
| **Low** | CRUD endpoint, simple UI component, config change | 8–40h |
| **Medium** | Multiple endpoints, state machine, integration with 1 external service | 40–120h |
| **High** | Real-time processing, multi-service orchestration, ML, hardware SDK, payment gateway | 120–400h |

### Calendar Conversion
```
Calendar Days = (Dev + Test Hours) / 8h × Parallelization Factor × Risk Multiplier
```
- Parallelization factor: 0.5 (2 devs can work in parallel) to 1.0 (single dev, sequential)
- Risk multiplier: 1.2 (low risk) to 1.5 (high risk — hardware SDK, external API approval)

### Test Hours Ratio
- Low complexity: ~30% of dev hours
- Medium complexity: ~40–50% of dev hours
- High complexity: ~50–60% of dev hours
