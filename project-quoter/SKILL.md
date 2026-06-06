---
name: project-quoter
description: "Requirements decomposition, phased effort estimation, budgeting, and client-facing proposal/quote HTML generation for software projects."
allowed-tools: read,write,edit,exec
user-invocable: true
---

# Project Quoter

End-to-end software project requirements analysis → modular breakdown → effort estimation → pricing → client-facing proposal HTML. Reusable across any new project.

## Position in Skill Chain

```
market-research → project-planner → project-quoter → ⏸️ CUSTOMER DECISION → project-implementation → project-uat
```

**Critical:** Quoting happens AFTER planning but BEFORE implementation. The quote is the decision gate — it tells the customer what to expect and gives them a clear Go/No-Go choice. Never implement before the quote is signed.

---

## Core Workflow (7 Stages)

1. Requirements Capture → `Draft scope.md`
2. Modular Breakdown → E01–E0N per-module plans
3. Effort Estimation (calibrated) → per-module hours
4. Pricing Strategy (tiered) → Good / Better / Best options
5. Master Aggregation → `MASTER_SUMMARY.md`
6. Project Plan HTML → `PROJECT_PLAN.html`
7. Proposal/Quote HTML → `PROPOSAL.html`

Each stage builds on the previous. Never skip. Every output is both a client-facing asset and internal engineering artifact.

---

## Stage 1: Requirements Capture

**Goal:** Produce a categorized feature catalog the client can review.

**Process:**
- List every feature mentioned or implied; one row per capability
- Group into 3 phases (Core MVP / Growth / Future) with commitment levels: Contracted / Conditional / Roadmap
- For each feature, write: Business Objective, Functional Requirements, Non-Functional Requirements, Technical Implementation Notes, Acceptance Criteria, Dependencies & Risks
- Flag KPI gates for Phase 2+ activation (e.g., ">500 active members required")

**Output:** `plan/Draft scope.md`

---

## Stage 2: Modular Breakdown

**Goal:** One detailed plan file per module, each independently estimable and buildable.

**Process:**
- Split scope into modules; each gets an `E0X` ID
- For each module, write:
  - User stories per persona
  - Technical tasks with input/output JSON schemas
  - Unit test cases per task (at least 5 per task)
  - Behavior/integration test scenarios (10+ per module)
  - Dependencies on other modules
  - Risk items with buffer days
- Estimate each task with t-shirt sizing (see Stage 3)

**Output:** `plan/E01-xxx.md` through `plan/E0N-yyy.md`

---

## Stage 3: Effort Estimation (CALIBRATED)

### 3.1 T-Shirt Sizing with Narrow Ranges

The old "Low ≤40h" approach had 5x variance per task. Use narrower buckets:

| Size | Hours | Typical Tasks |
|------|-------|---------------|
| **XS** | 1–8h | Single endpoint, schema tweak, CSS fix |
| **S** | 8–20h | Simple CRUD module, auth middleware |
| **M** | 20–40h | Full feature with tests, multi-table schema |
| **L** | 40–80h | Complex feature, payment integration, file upload |
| **XL** | 80–160h | Major subsystem, multi-module orchestration |
| **XXL** | 160–400h | Platform-level feature, real-time, ML |

### 3.2 Reference-Class Forecasting

For each module, find the closest completed reference module:

```
"This auth module (E01) is similar to ELY2's E01 which took 85h → estimate 75–95h"
"This sessions module (E02) is like ELY2's E02 which took 120h → estimate 100–140h"
```

After each project, log actual hours per module to `plan/ACTUAL_VS_ESTIMATE.md` for future calibration.

### 3.3 Integration Tax

Multi-module projects have cross-cutting overhead:

| Project Size | Integration Tax |
|-------------|-----------------|
| 1–3 modules | 10% |
| 4–8 modules | 18% |
| 9–15 modules | 22% |
| 16+ modules | 25% |

This covers: shared auth flows, cross-module API contracts, database migrations that span schemas, end-to-end test setup, and deployment orchestration.

### 3.4 Phase 0 Costs (Explicit Line Item)

These are REAL work, not padding:

| Phase 0 Item | Typical Hours |
|-------------|---------------|
| Database setup + migrations | 4–8h |
| Test infrastructure (supertest, helpers, scripts) | 4–8h |
| CI/CD pipeline setup | 4–6h |
| Dev environment documentation | 2–4h |
| **Total Phase 0** | **14–26h** |

### 3.5 QA Hour Calculation

```
Per-module QA = dev_hours × qa_ratio × complexity_multiplier

Simple modules (CRUD, schema): qa_ratio = 0.3
Medium modules (auth, file upload): qa_ratio = 0.4
Complex modules (payments, real-time): qa_ratio = 0.5
Module with heavy integration surface: qa_ratio += 0.1

Example: Auth module (85h dev, medium complexity):
  QA = 85 × 0.4 = 34h
```

### 3.6 Calendar Days

```
Calendar days = (dev + qa) / daily_capacity × team_size_factor × risk_multiplier

daily_capacity = 6 (productive hours per person per day)
team_size_factor = 1.0 (1 dev) / 0.6 (2 devs) / 0.45 (3 devs)
risk_multiplier = 1.15 (low risk) / 1.25 (medium) / 1.4 (high — unknown tech, tight deadline)
```

### 3.7 Unknown-Unknowns (Explicit Line)

Never hide contingency in per-task estimates. Show it as a line item:

```
Known scope:            620h
Integration tax (18%):  112h
Phase 0:                 20h
Unknown-unknowns (10%):  75h
─────────────────────────────
Total estimate:         827h (range: 660–990h at 90% confidence)
```

### 3.8 Historical Calibration

After each project, calculate the error ratio:

```
Calibration factor = Σ(actual hours) / Σ(estimated hours)

After project 1: 1.15 (underestimated by 15%)
After project 2: 1.08 (underestimated by 8%)
After project 3: 0.97 (overestimated by 3%)

Running calibration: 1.07 → apply to next project
```

Store calibration data in `plan/CALIBRATION_LOG.md` across all projects.

---

## Stage 4: Pricing Strategy (TIERED + NEGOTIATION-READY)

### 4.1 Three Pricing Levers

| Lever | What | Example |
|-------|------|---------|
| **Cost-plus** | What it costs us + margin | 827h × $100 × 1.4 = $116K |
| **Value-based** | What it saves/makes the customer × fraction | $50K/yr revenue × 18 months = $75K |
| **Market benchmark** | What similar projects cost ±20% | Upwork median for similar: $55K |

**Use all three.** The final price should make sense from every angle.

### 4.2 Tiered Options (Good-Better-Best)

Give the customer choice. They negotiate against themselves:

| Tier | Scope | Price | Margin | Target |
|------|-------|-------|--------|--------|
| **Basic** | 60% — core MVP only, no polish | $35K | 40% | Budget-conscious, fast-to-close |
| **Standard** | 100% — full spec with UX | $58K | 45% | Best value, recommended |
| **Premium** | 100% + warranty + training + 3mo support | $85K | 55% | High-touch, premium clients |

**Psychological anchoring:** The Standard tier should be the obvious choice. Basic feels too limited. Premium feels expensive but aspirational. Standard is "just right."

### 4.3 Sweet Spot Calculator

The customer's "easy yes" zone:

```
Sweet spot = 2–4 months of expected platform monthly revenue

If platform generates $15K/month → sweet spot = $30K–$60K
Above $60K → needs strong ROI justification
Below $30K → easy yes, "worth the risk"
```

### 4.4 Market Benchmarking Sources

| Source | Use For |
|--------|---------|
| Upwork Pro / Toptal | Freelance team rates |
| Clutch.co / GoodFirms | Agency project ranges |
| Similar projects on GitHub | Scope comparison |
| Local competitors | Regional pricing |

Quote within ±20% of market median unless you have a clear differentiator.

---

## Stage 4B: Negotiation Strategy (BARGAINING-READY)

### 5.1 Three Prices, Not One

Every proposal has three prices internally:

| Price | Purpose | Calculation |
|-------|---------|-------------|
| **List Price** | What the proposal shows | Cost × 1.5–2.0 (room to negotiate) |
| **Target Price** | What you actually want | Cost × 1.35–1.5 (healthy margin) |
| **Floor Price** | Walk-away point | Cost × 1.2 (absolute minimum margin) |

**Never reveal your floor.** Never go below floor.

### 5.2 Bargaining Playbook

**When customer says "too expensive":**

| Tactic | Response |
|--------|----------|
| "Can you do better?" | "I can offer $X if we reduce scope [trade feature Y for discount]" |
| "X quoted me less" | "Let me show you what they're likely missing [integration, testing, warranty]" |
| "My budget is only $X" | "Let's look at the Basic tier — core MVP within your budget" |
| "Can you do $X cash?" | "Payment terms affect price. 50% upfront = 5% discount" |
| Silence after quote | Don't drop price unsolicited. Wait for their counter. |

### 5.3 Scope Trade-Offs (Never Discount Without Taking Something)

```
Customer: "Can you do $45K instead of $58K?"

WRONG: "OK, $45K." (you just lost $13K margin for nothing)

RIGHT: "At $45K, we'd scope to the Basic tier — core MVP without
        admin dashboard, without referral system, without imaging.
        Or keep full scope at $55K with 3% early-signing discount."
```

**Every discount costs scope.** This trains customers to value features, not just negotiate price.

### 5.4 Negotiation Margin by Phase

| Phase | List Price Margin | Why |
|-------|-------------------|-----|
| Phase 1 (Contracted) | +25% above target | Most bargaining happens here |
| Phase 2 (Conditional) | +15% above target | Less negotiation since it's "future" |
| Phase 3 (Roadmap) | Not priced | Strategic direction only |

### 5.5 Payment Terms as Negotiation Tool

| Offer | Discount | Win For Us |
|-------|----------|------------|
| 50% upfront | 5% off | Cash flow, commitment |
| Full upfront | 10% off | Zero risk, locked in |
| Monthly retainer (12mo) | 15% off | Recurring revenue, long-term relationship |

### 5.6 Walk-Away Rules

Walk away when:
- Customer demands >20% off list price without scope reduction
- Customer refuses to sign any contract
- Customer's budget is < floor price for even the Basic tier
- Customer shows red flags (unreasonable demands, scope creep early, payment delays)

**A bad deal is worse than no deal.**

---

## Stage 5: Master Aggregation

**Goal:** One summary file aggregating all modules with totals.

**Process:**
- Sum hours across all modules per phase, per tier
- Calculate calendar days with parallelization assumptions
- State critical paths
- Provide team composition recommendations per phase
- Estimate infrastructure monthly cost
- List risks with probability/impact/mitigation
- Show internal pricing (cost / target / list / floor) for each tier
- Provide quick-start build sequence

**Output:** `plan/MASTER_SUMMARY.md`

---

## Stage 6: Project Plan HTML

**Goal:** Client-facing engineering overview with timeline visualization.

**Process:**
- Generate self-contained HTML with embedded CSS
- Include: summary cards, timeline bar, module tables, team composition
- Show the 3-tier options prominently (Good / Better / Best)
- Module tables show: ID, name, complexity badge, dev hrs, calendar days, dependencies
- Color-coded: Blue (Phase 1), Purple (Phase 2), Green (Phase 3)

**Output:** `plan/PROJECT_PLAN.html`

---

## Stage 7: Proposal/Quote HTML

**Goal:** Formal business proposal for client signature.

**Process:**
- Cover page with project name, version, date, confidentiality
- Executive summary with key numbers (weeks, cost, phases)
- **Tier comparison table** — Good / Better / Best with scope and price
- "How It Works" workflow diagram
- Expected business outcomes grid with ROI calculation
- Commitment summary (Contracted / Conditional / Roadmap)
- Payment milestones with percentages
- Inclusions / exclusions table
- **Value justification** — comparison vs. traditional dev, vs. no-code, vs. in-house

**Pricing rules:**
- Phase 1: Fixed price, binding. 50% start + 25% midpoint + 25% go-live
- Phase 2: Conditional estimate. Triggered by KPI gates.
- Phase 3: Not priced. Strategic direction.

**Output:** `plan/PROPOSAL.html`

---

## Pricing Formula (Internal)

```
Cost = Dev Hours × Internal Rate + Phase 0 + Infrastructure
Target Price = Cost × 1.40 (40% gross margin)
List Price = Cost × 1.65 (shown to customer, room to negotiate)
Floor Price = Cost × 1.20 (walk-away point)

Where:
  Internal Rate = $40–60/hr (what YOU pay yourself/sub-agents)
  Customer-Facing Rate = $80–150/hr (what the market bears)
```

### Margin Structure

| Tier | Target Margin | Floor Margin |
|------|--------------|--------------|
| Basic | 40% | 20% |
| Standard | 45% | 25% |
| Premium | 55% | 30% |

Round final prices to clean numbers (nearest $1K for small projects, $5K for medium, $10K for large).

---

## Decision Gate (Critical)

After delivering the proposal:

```
⏸️ CUSTOMER REVIEW PERIOD (3–7 days typical)

Go: Customer signs → proceed to project-implementation
No-Go: Customer declines → archive, log reasons, learn
Hold: Customer wants changes → adjust scope/tier, re-quote

NEVER start implementation without signed proposal.
```

---

## Iterative Refinement

After each project:
1. Log actual hours to `CALIBRATION_LOG.md`
2. Update calibration factor
3. Review which tier the customer chose (adjust defaults)
4. Review which negotiation tactics worked
5. Update market benchmark data
6. Refine t-shirt sizing assumptions

---

## Skill Execution

When invoked for a new project:
1. Gather requirements from conversation context
2. Create project directory at `~/projects/<project>/plan/`
3. Execute Stages 1–7 sequentially
4. After each stage, present output for review (unless user says "go ahead")
5. Final output: `PROPOSAL.html` with 3 tiers and negotiation-ready pricing

---

## Antipatterns

| Antipattern | Fix |
|-------------|-----|
| Quoting after implementation | Quote is the decision gate — do it between plan and code |
| Single price with no tiers | Always offer 3: Good/Better/Best |
| Cost-plus only, no value justification | Show ROI: "this costs $58K but saves/generates $50K/year" |
| Discounting without scope reduction | Every discount costs a feature |
| Revealing floor price | List price is what you show. Floor is what you know. |
| "Low ≤40h" estimation | Use XS/S/M/L/XL/XXL with narrow ranges |
| Hiding contingency in task estimates | Show unknown-unknowns as explicit line item |
| No calibration feedback loop | Log actuals, adjust estimates for next project |
