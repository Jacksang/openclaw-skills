# Master Summary Template

Aggregates all module plans into one summary. Copy to `plan/MASTER_SUMMARY.md`.

## Structure

```markdown
# {PROJECT} — Master Summary: All Modules, Estimates & Launch Plan

> **Date:** {DATE}
> **Status:** Ready for Review
> **Total Module Files:** {N} (1 Scope + {N-1} Module Details)
> **Total Lines:** {count} across all plan documents

---

## 1. Module Overview & Aggregate Estimates

### Phase 1 — MVP Core (Contracted)

| Module | Description | Dev (hrs) | Test (hrs) | Calendar Days |
|---|---|---|---|---|
| E0X | {Name} | XX | XX | X |
| **Phase 1 Subtotal** | | **XXX** | **XXX** | **XX** |

> **Phase 1 Critical Path:** {description}
> **Parallelization:** {which modules can run concurrently}
> **Realistic Phase 1 Timeline (N devs): ~X weeks**

### Phase 2 — Growth (Conditional)

| Module | Description | Dev (hrs) | Test (hrs) | Calendar Days |
|---|---|---|---|---|
| E0X | {Name} | XX | XX | X |
| **Phase 2 Subtotal** | | **XXX** | **XXX** | **XX** |

### Phase 3 — Future (Roadmap)

| Module | Description | Dev (hrs) | Test (hrs) | Calendar Days |
|---|---|---|---|---|
| E0X | {Name} | — | — | ~XX |
| **Phase 3 Subtotal** | | — | — | ~XX |

---

## 2. Grand Total

| Metric | Value |
|---|---|
| **Total Dev Hours** | **XXX hrs** |
| **Total Test Hours** | **XXX hrs** |
| **Total Dev + Test Hours** | **XXX hrs** |
| **Total Calendar Days (sequential)** | **XX days** |
| **Total Calendar Weeks (with parallelization)** | **~X weeks** |

### Effort by Category

| Category | Dev Hours | Test Hours | % of Total |
|---|---|---|---|
| 🔴 Contracted (P1) | XXX | XXX | XX% |
| 🟡 Conditional (P2) | XXX | XXX | XX% |
| 🟢 Roadmap (P3) | — | — | — |

---

## 3. Task Count Summary

| Module | API Tasks | Frontend Tasks | Infra Tasks | Util Tasks | Total |
|---|---|---|---|---|---|
| E01 | X | X | X | X | X |
| ... | | | | | |
| **Total** | **XX** | **XX** | **XX** | **XX** | **XX** |

---

## 4. Risk Factors & Mitigations

| Risk | Probability | Impact | Mitigation |
|---|---|---|---|
| {Risk description} | Low/Med/High | Low/Med/High | {Concrete mitigation step} |

(Minimum 8 risks covering technical, external dependency, scope, timeline)

---

## 5. Infrastructure Cost Estimate (Monthly)

| Service | Est. Monthly Cost (USD) |
|---|---|
| {Service name} | $X – $Y |
| **Total Monthly Cloud** | **$XX – $YY** |

Add a buffer: if the bare minimum is $20–40, state "$20–40 depending on usage" not "$5–15".

---

## 6. Quick Start: What to Build First

1. **Week 1-2:** Infrastructure provisioning
2. **Week 3-4:** Core APIs
3. ...

---

## 7. Deliverables Checklist

- [x] `plan/Draft scope.md`
- [x] `plan/E01-xxx.md`
- [x] `plan/E02-yyy.md`
- ...
- [x] `plan/MASTER_SUMMARY.md`
```

## Aggregation Rules

- **Sum dev/test hours** directly from each module plan. No re-estimating at summary level.
- **Calendar days** factor in parallelization: if two 8-day modules can run in parallel, they count as 8 days, not 16.
- **Critical path** is the longest chain of dependent modules that cannot be parallelized.
- **Risk factors** are collected from module-level risks plus cross-cutting concerns (team availability, external approvals).
- **Infrastructure costs** must be honest and include buffer. Underquoting infra cost erodes trust.
