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

| Module | Description | Work (h) | AI wall-clock (h) | Calendar Wks |
|---|---|---|---|---|
| E0X | {Name} | XX | XX | X |
| **Phase 1 Subtotal** | | **XXX** | **XXX** | **XX** |

> **Dual-track (internal):** Human = gate-based hours @ **$250 SGD/hr** (not dev-ratio). AI = wall-clock @ $9.50 SGD/hr. See dual-track-estimation.md gate catalog.
> **human_ai_ratio:** ~0.03–0.15 expected. **Customer timeline:** `customer_committed_weeks` only; +20% buffer internal.

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
| **Total Work Hours** | **XXX hrs** |
| **Human Gate Hours** | **XXX hrs** @ $250 SGD |
| **AI Wall-Clock Hours** | **XXX hrs** @ $9.50 SGD |
| **human_ai_ratio** | **0.XX** (flag if >0.25) |
| **Internal Cost (dual-track)** | **$XX,XXX SGD** |
| **customer_committed_weeks** | **~X wks** (client-facing) |
| **internal_planned_weeks** | **~X wks** (+20% buffer — INTERNAL) |

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

## 5. Infrastructure Cost Estimate

| Environment | Est. Cost |
|---|---|
| **Local Docker (dev/demo/UAT/delivery)** | **$0 – minimal** (customer hardware only) |
| **Cloud (optional Phase B — AWS etc.)** | **$XX – $YY SGD/month** (only after local sign-off + customer cloud approval) |

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
- [x] `plan/assets/timeline-milestones.png` (+ workflow, gantt — regenerate on timeline change)
```

## Aggregation Rules

- **AI hours** from module plans; **human hours** from gate catalog (not module ratio); cost at $250 human / $9.50 AI SGD
- **customer_committed_weeks** for client; **internal_planned_weeks** = base × 1.20 (never share)
- **Critical path** is the longest chain of dependent modules that cannot be parallelized.
- **Risk factors** are collected from module-level risks plus cross-cutting concerns (team availability, external approvals).
- **Infrastructure costs** must be honest and include buffer. Underquoting infra cost erodes trust.
