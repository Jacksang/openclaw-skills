---
name: project-quoter
description: "Effort estimation, budgeting, tiered pricing, negotiation strategy, and client-facing proposal/quote HTML generation for software projects. Use when the customer needs a quote, proposal, price, or effort estimate after project planning is complete, or when preparing a Go/No-Go decision document."
allowed-tools: read,write,edit,exec
user-invocable: true
---

# Project Quoter

Planner artifacts → modular technical breakdown → effort estimation → pricing → client-facing proposal HTML. Reusable across any new project.

## Position in Skill Chain

```
project-planner → project-quoter → ⏸️ CUSTOMER DECISION → project-implementation → project-uat → project-delivery
```

**Critical:** Quoting happens AFTER planning but BEFORE implementation. The quote is the decision gate — it tells the customer what to expect and gives them a clear Go/No-Go choice. Never implement before the quote is signed.

**This skill does not re-create planning artifacts.** User stories, behavior tests, and the draft scope are produced by `project-planner` and consumed here. The quoter adds: technical task breakdown, estimates, pricing, and client documents.

---

## Templates & References

Read the matching reference file before executing each stage — these are battle-tested formats, do not improvise:

| Stage | File |
|-------|------|
| Stage 1 (scope fallback) | `references/templates/draft-scope-template.md` |
| Stage 2 (module plans) | `references/templates/module-plan-template.md` |
| Stage 6 (master summary) | `references/templates/master-summary-template.md` |
| Stage 7 (project plan HTML) | `references/templates/project-plan-html-template.md` |
| Stage 8 (proposal HTML) | `references/templates/proposal-html-template.md` |
| Methodology deep-dive, pitfalls | `references/methodology.md` |
| Worked example | `references/examples/ely-reference.md` |
| Cross-project calibration data | `references/calibration-log.md` |

---

## Core Workflow (8 Stages)

0. Commercial Inputs → rates, currency, margins
1. Verify Planning Inputs → planner deliverables present
2. Modular Technical Breakdown → E01–E0N per-module plans
3. Effort Estimation (calibrated) → per-module hours
4. Pricing Strategy (tiered) → Good / Better / Best options
5. Negotiation Strategy → list/target/floor, playbook
6. Master Aggregation → `MASTER_SUMMARY.md`
7. Project Plan HTML → `PROJECT_PLAN.html`
8. Proposal/Quote HTML → `PROPOSAL.html`

Each stage builds on the previous. Never skip. After each stage, present output for review (unless the user says "go ahead").

---

## Stage 0: Commercial Inputs

Before any numbers, ask the user (do not assume):

- **Currency** (e.g. USD, SGD)
- **Internal rate** — what you pay yourself/sub-agents (default $40–60/hr)
- **Customer-facing rate** — what the market bears (default $80–150/hr)
- **Target margin** if different from defaults below

Record these at the top of `MASTER_SUMMARY.md` so every later calculation is traceable.

---

## Stage 1: Verify Planning Inputs

**Goal:** Confirm the planner's deliverables exist before estimating.

**Required inputs (from `project-planner`):**
- `plan/Draft scope.md` — signed-off feature catalog with phases and commitment levels
- `plan/E0X-[role]-stories.md` — user stories per epic/role
- `tests/E0X-[role]-tests.md` — behavior test cases
- `plan/ARCHITECTURE.md` — tech stack, data model, API conventions

**If missing:** run `project-planner` first. For quote-only engagements where full planning isn't warranted, do a lightweight capture using `references/templates/draft-scope-template.md` and state in the proposal that estimates are pre-planning class (±50% confidence).

**Never edit the customer's Draft scope.**

---

## Stage 2: Modular Technical Breakdown

**Goal:** One detailed plan file per module, each independently estimable and buildable. Modules reuse the planner's epic IDs (`E0X`) — do not invent a second numbering scheme.

**Process (per module, using `module-plan-template.md`):**
- Reference (don't duplicate) the planner's user stories and behavior tests by ID
- Add technical tasks with input/output JSON schemas, consistent with `plan/ARCHITECTURE.md`
- Add unit test cases per task (at least 5 per task)
- Map dependencies on other modules
- List risk items with buffer days
- Size each task with t-shirt sizing (see Stage 3)

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

For each module, find the closest completed module in `references/calibration-log.md`:

```
"This auth module (E01) is similar to [past project]'s E01 which took 85h → estimate 75–95h"
"This sessions module (E02) is like [past project]'s E02 which took 120h → estimate 100–140h"
```

If the calibration log is empty (first project), estimate bottom-up from t-shirt sizes and widen the confidence range.

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

Log per-project actuals to the project's `plan/ACTUAL_VS_ESTIMATE.md`, then roll them up into `references/calibration-log.md` **inside this skill's folder** — that file persists across projects; per-project folders do not. Read it at the start of every Stage 3.

---

## Stage 4: Pricing Strategy (TIERED + NEGOTIATION-READY)

### 4.1 Three Pricing Levers

| Lever | What | Example |
|-------|------|---------|
| **Cost-plus** | What it costs us + margin | 827h × $100 × 1.4 = $116K |
| **Value-based** | What it saves/makes the customer × fraction | $50K/yr revenue × 18 months = $75K |
| **Market benchmark** | What similar projects cost ±20% | Upwork median for similar: $55K |

**Use all three.** The final price should make sense from every angle.

If the **market-research-agent** skill was run for this customer, use its findings for the value-based and market-benchmark levers (competitor pricing, market size, customer willingness to pay).

### 4.2 Tiered Options (Good-Better-Best)

Give the customer choice. They negotiate against themselves:

| Tier | Scope | Margin | Target |
|------|-------|--------|--------|
| **Basic** | 60% — core MVP only, no polish | 40% | Budget-conscious, fast-to-close |
| **Standard** | 100% — full spec with UX | 45% | Best value, recommended |
| **Premium** | 100% + warranty + training + 3mo support | 55% | High-touch, premium clients |

Priceable add-ons for any tier: formal penetration test (the baseline vulnerability scan is always included in delivery — the pen-test is the paid upgrade), extended support, SLA upgrades.

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

## Stage 5: Negotiation Strategy (BARGAINING-READY — INTERNAL ONLY)

Everything in this stage is internal. **None of it may appear in any client-facing document.**

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

## Stage 6: Master Aggregation

**Goal:** One summary file aggregating all modules with totals. Use `master-summary-template.md`.

**Process:**
- Record Stage 0 commercial inputs (rates, currency)
- Sum hours across all modules per phase, per tier
- Calculate calendar days with parallelization assumptions
- State critical paths
- Provide team composition recommendations per phase
- Estimate infrastructure monthly cost (apply 2–3× buffer — clients always exceed naive infra estimates)
- List risks with probability/impact/mitigation
- Show internal pricing (cost / target / list / floor) for each tier
- Provide quick-start build sequence

**Output:** `plan/MASTER_SUMMARY.md` — **INTERNAL document. Never send to the client or copy its margin/floor/rate data into client HTML.**

---

## Stage 7: Project Plan HTML

**Goal:** Client-facing engineering overview with timeline visualization. Use `project-plan-html-template.md`.

**Process:**
- Generate self-contained HTML with embedded CSS
- Include: summary cards, timeline bar, module tables, team composition
- Show the 3-tier options prominently (Good / Better / Best)
- Module tables show: ID, name, complexity badge, dev hrs, calendar days, dependencies
- Color-coded: Blue (Phase 1), Purple (Phase 2), Green (Phase 3)
- **List prices only** — no internal rates, margins, target or floor prices

**Output:** `plan/PROJECT_PLAN.html`

---

## Stage 8: Proposal/Quote HTML

**Goal:** Formal business proposal for client signature. Use `proposal-html-template.md`.

**Process:**
- Cover page with project name, version, date, confidentiality
- Executive summary with key numbers (weeks, cost, phases)
- **Tier comparison table** — Good / Better / Best with scope and price
- "How It Works" workflow diagram
- Expected business outcomes grid with ROI calculation
- Commitment summary (Contracted / Conditional / Roadmap)
- Payment milestones with percentages
- Inclusions / exclusions table — list every third-party cost (domain, SSL, messaging fees) explicitly
- **Value justification** — comparison vs. traditional dev, vs. no-code, vs. in-house

**Pricing rules:**
- Phase 1: Fixed price, binding. 50% start + 25% midpoint + 25% go-live
- Phase 2: Conditional estimate. Triggered by KPI gates.
- Phase 3: Not priced. Strategic direction.
- **List prices only.** Floor/target/margins stay in `MASTER_SUMMARY.md`.

**Iteration:** when the client requests changes, fix the specific HTML file in place and bump the version — never regenerate from scratch (you'd lose prior fixes).

**Output:** `plan/PROPOSAL.html`

---

## Pricing Formula (Internal)

```
Cost = Dev Hours × Internal Rate + Phase 0 + Infrastructure
Target Price = Cost × 1.40 (40% gross margin)
List Price = Cost × 1.65 (shown to customer, room to negotiate)
Floor Price = Cost × 1.20 (walk-away point)

Where:
  Internal Rate = from Stage 0 (default $40–60/hr)
  Customer-Facing Rate = from Stage 0 (default $80–150/hr)
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

## Post-Signing Change Orders

Scope changes after signing are inevitable. Handle them commercially, never silently:

1. **Log every request** in `plan/CHANGE_REQUESTS.md` (ID `CR-NN`, description, requester, date).
2. **Triage** using the canon in project-implementation ("Bug vs Change Request Triage"): defects are free; specification gaps ≤ XS are goodwill; everything else is billable new scope.
3. **Mini-quote billable changes**: t-shirt size × customer-facing rate (+ QA ratio + integration impact). Apply a minimum charge (e.g. 4h) — tiny changes still cost coordination.
4. **Written approval before work starts** — a one-paragraph change order referencing the CR ID, price, and delivery impact (calendar days added).
5. **Delta epics**: a substantial new feature becomes a new epic run through the mini-pipeline — planner Stages 3–6 for the delta only (reusing glossary, design system, architecture) → this change order → a new implementation phase → targeted UAT.
6. **Batching**: when several CRs accumulate, propose bundling them as a minor release — this is exactly what the Phase 2 (Conditional) structure exists for. Re-enter the chain from the planner with the delta scope.

**Every discount costs scope; every scope addition costs money.** The same discipline in both directions.

---

## Iterative Refinement

After each project:
1. Log actual hours to `plan/ACTUAL_VS_ESTIMATE.md`, roll up into `references/calibration-log.md`
2. Update calibration factor
3. Review which tier the customer chose (adjust defaults)
4. Review which negotiation tactics worked
5. Update market benchmark data
6. Refine t-shirt sizing assumptions

---

## Skill Execution

When invoked for a new project:
1. Run Stage 0 (commercial inputs) and Stage 1 (verify planner deliverables)
2. Work in the project directory (default `~/projects/<project>/plan/`, or the current workspace)
3. Execute Stages 2–8 sequentially, reading the matching reference template before each output stage
4. After each stage, present output for review (unless user says "go ahead")
5. Final output: `PROPOSAL.html` with 3 tiers and negotiation-ready pricing

---

## Antipatterns

| Antipattern | Fix |
|-------------|-----|
| Re-creating user stories/tests the planner already made | Reference planner artifacts by ID; quoter adds tasks + estimates only |
| Quoting after implementation | Quote is the decision gate — do it between plan and code |
| Single price with no tiers | Always offer 3: Good/Better/Best |
| Cost-plus only, no value justification | Show ROI: "this costs $58K but saves/generates $50K/year" |
| Discounting without scope reduction | Every discount costs a feature |
| Revealing floor price | List price is what you show. Floor is what you know. |
| Internal pricing in client HTML | MASTER_SUMMARY is internal; client docs show List prices only |
| "Low ≤40h" estimation | Use XS/S/M/L/XL/XXL with narrow ranges |
| Hiding contingency in task estimates | Show unknown-unknowns as explicit line item |
| No calibration feedback loop | Log actuals to the skill's calibration-log.md, adjust next project |
