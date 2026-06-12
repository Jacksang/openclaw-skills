# Dual-Track Estimation — Gate-Based Human Hours + AI Agent Hours

Internal methodology for accurate costing and customer-facing accelerated timelines. **Cost split is internal only.** Customer sees list price + faster delivery narrative — never human/AI breakdown, never the internal timeline buffer.

**Core principle:** AI agents execute the pipeline automatically with minimal human touch. **Human hours are not `(1 − ai_share) × module_work`.** They are built **bottom-up from review gates** defined across the project skills — only where a human checkpoint, customer interaction, or coordinator judgment is explicitly required.

## Default Commercial Inputs

| Input | Default |
|-------|---------|
| Currency | **SGD** |
| Human rate | **$250 SGD/hr** — senior review, customer-facing gates, integration escalation |
| AI agent rate | **$9.50 SGD/hr** (= **$7 USD/hr**) — wall-clock agent execution |
| AI performance baseline | Small-model class: gpt-5.3 / sonnet-4.5 / qwen3.6-35B-a3b level |

Override only when the user specifies different rates.

---

## Step 1: AI Track (Module Work → Agent Hours)

Start from t-shirt sizing + calibration. Label `module_work_h` per module — total effort to deliver correctly at the AI baseline.

### AI-executable share

| Module type | Share of module_work_h |
|-------------|------------------------|
| Simple CRUD, schema, CSS | 80% |
| Standard features, forms, lists | 65% |
| Auth, file upload, multi-table | 55% |
| Payments, real-time, hardware/API-heavy | 45% |

```
ai_work_h       = module_work_h × ai_executable_share
ai_wall_clock_h = ai_work_h × 1.5    ← small-model retry/rework factor
```

**AI track total** = Σ `ai_wall_clock_h` + AI-assistable QA verification (~30% of QA hours from Stage 3.5).

**Do not assign the remainder `(1 − ai_share) × module_work_h` to humans** — that work is still done by agents; humans do not sit through implementation hour-for-hour.

---

## Step 2: Human Track (Gate-Based — Bottom-Up)

Enumerate **only** activities where a human must act. Use the gate catalog below; adjust counts to project scope (epic count, phase count, tier).

### Human effort catalog (what humans actually do)

| Gate | Skill | Typical hours | When |
|------|-------|---------------|------|
| Scope / epic confirmation | project-planner | 1–2h | Once after Stage 2 |
| Validation report review + fix direction | project-planner | 1–3h per epic | Per epic after Stage 6 |
| Design approval session with customer | project-planner | 2–4h | Stage 9 |
| Commercial inputs + stage reviews | project-quoter | 2–4h | Quoting |
| Proposal walkthrough / negotiation | project-quoter | 2–6h | Decision gate |
| Phase 0 / Phase N gate sign-off | project-implementation | 0.5–1h per phase | Coordinator review |
| Midpoint customer demo | project-implementation | 2–3h | Once per contracted phase |
| Integration failure escalation | project-implementation | 2–4h per phase | **Budget only if complex** — not every phase |
| Security / NFR gate review | project-implementation | 2–4h | Once before UAT |
| UAT Human Round 2 execution | project-uat | 8–20h | Scales with epic count × ~1–2h |
| Validation escalate (3+ rounds) | project-uat | 2–4h | If needed |
| Local delivery demo + sign-off | project-delivery | 2–4h | Part A |
| Cloud go-live approval meeting | project-delivery | 1–2h | Part B only |
| Training delivery | project-delivery | 2–8h | If sold in tier |
| Ad-hoc customer comms buffer | all | 4–8h | Whole project |

```
human_gate_h = Σ (gate hours × count for this project)
human_total_h = human_gate_h
```

**Optional integration-debug reserve** (not gate time, but real): add 3–8h **project-wide** for payment/auth/hardware modules only — not per-module ratio.

### Sanity check — human : AI ratio

```
human_ai_ratio = human_total_h / ai_wall_clock_total
```

| Ratio | Interpretation |
|-------|----------------|
| **0.03 – 0.15** | Expected — agents run; human at gates only |
| **0.15 – 0.25** | Heavy customer touch or many epics — justify |
| **> 0.25** | Likely over-counting human — re-audit gates; you may be using the old `(1−ai_share)` mistake |

Record `human_ai_ratio` in `MASTER_SUMMARY.md` internal section.

---

## Step 3: Project Roll-Up

Add Phase 0, integration tax, unknown-unknowns on the **AI track** primarily. Human gates are additive (fixed line items), not taxed again.

Example (Phase 1 Contracted, 7 epics):

| Track | Hours | Rate | Cost |
|-------|-------|------|------|
| Human (gates) | ~45h | $250 SGD/hr | ~$11,250 |
| AI wall-clock | ~1,150h | $9.50 SGD/hr | ~$10,925 |
| **Internal cost subtotal** | | | **~$22,175** |

(Contrast with old model: 525h human @ $50 was wrong on both rate and hour count.)

---

## Step 4: Calendar / Timeline

```
daily_ai_capacity    = 12 wall-clock hrs/day   ← agents drive the schedule
daily_human_capacity = 2–4 hrs/day on gates    ← human work is bursty, not full-time parallel

internal_base_weeks  = ai_wall_clock_total / (daily_ai_capacity × 5)
                       adjusted for critical path + parallelization
                       + gate weeks if a gate blocks the chain (e.g. design approval wait)

internal_planned_weeks = internal_base_weeks × 1.20   ← INTERNAL ONLY; never show customer
customer_committed_weeks = round(internal_base_weeks)
```

**Customer-facing:** show `customer_committed_weeks` + optional "traditional all-human team" comparison. **Never** disclose buffer or human/AI split.

**Traditional comparison (copy only):**
```
traditional_weeks ≈ (Σ module_work_h + QA) / (6 × 5) × risk_multiplier
```

Regenerate all timeline PNGs when weeks change.

---

## Step 5: Pricing (Internal → List)

```
human_cost     = human_total_h × $250 SGD
ai_cost        = ai_wall_clock_total × $9.50 SGD
internal_cost  = human_cost + ai_cost + Phase 0 + infra
target_price   = internal_cost × target_margin
list_price     = target_price × list_room
```

Customer sees **list price (SGD)** + **customer_committed_weeks** only.

---

## MASTER_SUMMARY Internal Section (Required)

**"Dual-Track Effort (Internal — Do Not Share)"**

| Module | Work h | AI wall-clock h | Human gates (h) | Human $ @250 | AI $ @9.50 |
|--------|--------|-----------------|-----------------|--------------|------------|

Plus: gate breakdown table, `human_ai_ratio`, `internal_base_weeks`, `internal_planned_weeks`, `customer_committed_weeks`.

---

## Illustrations — PNG, Not Mermaid

Generate `plan/assets/timeline-milestones.png`, `workflow-execution.png`, `phase-gantt.png` via image generation. **Regenerate all three** whenever scope or timeline changes. Embed with `?v=N` cache-bust. No Mermaid in client docs.

---

## Antipatterns

| Wrong | Right |
|-------|-------|
| `human_h = module_work × (1 − ai_share)` | Bottom-up gate hours only |
| Human @ $50/hr | Human @ **$250 SGD/hr** for gate work |
| human_ai_ratio > 0.25 without justification | Re-audit — agents should carry bulk of work |
| Counting full QA as human | Most QA is agent-run; budget human for UAT Round 2 + gate review only |
