---
name: project-quoter
description: "Requirements decomposition, phased effort estimation, budgeting, and client-facing proposal/quote HTML generation for software projects."
allowed-tools: read,write,edit,exec
user-invocable: true
---

# Project Quoter

End-to-end software project requirements analysis → modular breakdown → effort estimation → pricing → proposal HTML generation. Reusable across any new project.

## Core Workflow (5 Stages)

1. Requirements Capture → `Draft scope.md`
2. Modular Breakdown → E01–E0N per-module plans
3. Master Aggregation → `MASTER_SUMMARY.md`
4. Project Plan HTML → `PROJECT_PLAN.html`
5. Proposal/Quote HTML → `PROPOSAL.html`

Each stage builds on the previous. Never skip. Every output is both a client-facing asset and internal engineering artifact.

## Stage 1: Requirements Capture

**Goal:** Produce a categorized feature catalog the client can review.

**Process:**
- List every feature mentioned or implied; one row per capability
- Group into 3 phases (Core MVP / Growth / Future) with commitment levels: Contracted / Conditional / Roadmap
- For each feature, write: Business Objective, Functional Requirements, Non-Functional Requirements, Technical Implementation Notes, Acceptance Criteria, Dependencies & Risks
- Flag KPI gates for Phase 2+ activation (e.g., ">500 active members required")

**Output:** `plan/Draft scope.md`
See `references/templates/draft-scope-template.md`

## Stage 2: Modular Breakdown

**Goal:** One detailed plan file per module, each independently estimable and buildable.

**Process:**
- Split scope into modules; each gets an `E0X` ID
- For each module, write:
  - User stories per persona (Prospect, Customer, Therapist, Admin, System)
  - Technical tasks with precise input/output JSON schemas
  - Unit test cases per task (at least 5 per task)
  - Behavior/integration test scenarios (10+ per module)
  - Effort estimates: dev hours, test hours, calendar days
  - Dependencies on other modules
  - Risk items with buffer days
- Estimate with realistic precision — not padded, but include explicit risk buffer

**Output:** `plan/E01-xxx.md` through `plan/E0N-yyy.md`
See `references/templates/module-plan-template.md`

**Estimation rules:**
- Complexity tags: Low (≤40h), Medium (40–120h), High (120–400h)
- Comms/integration overhead is separate from core dev
- QA hours are typically 40–60% of dev hours
- Calendar days = (dev+test)/8 × parallelization factor × risk multiplier (1.2–1.5)

## Stage 3: Master Aggregation

**Goal:** One summary file aggregating all modules with totals.

**Process:**
- Sum dev/test hours across all modules per phase
- Calculate calendar days with parallelization assumptions
- State critical paths
- Provide team composition recommendations per phase
- Estimate infrastructure monthly cost
- List risks with probability/impact/mitigation
- Provide quick-start build sequence

**Output:** `plan/MASTER_SUMMARY.md`
See `references/templates/master-summary-template.md`

## Stage 4: Project Plan HTML

**Goal:** Client-facing engineering overview with timeline visualization.

**Process:**
- Generate self-contained HTML with embedded CSS
- Include: summary cards (phases/modules/hours/weeks), timeline bar, per-phase module tables, Gantt chart, team composition table
- Module tables show: ID, name, description, complexity badge, dev hrs, test hrs, calendar days, dependencies
- Use color-coded phases: Blue (Phase 1), Purple (Phase 2), Green (Phase 3)
- Responsive design, print-friendly

**Output:** `plan/PROJECT_PLAN.html`
See `references/templates/project-plan-html-template.md`

## Stage 5: Proposal/Quote HTML

**Goal:** Formal business proposal for client signature.

**Process:**
- Cover page with project name, version, date, confidentiality
- Executive summary with key numbers (weeks, cost, phases)
- "How It Works" workflow diagram
- Expected business outcomes grid
- Commitment summary table (Contracted / Conditional / Roadmap) — **this is the pricing anchor**
- Phase-by-phase scope with deliverables checklist
- Delivery timeline table
- Methodology section (sprints, demos, UAT, change control, bug SLAs, warranty)
- Payment milestones with percentages
- Inclusions / exclusions table (what we deliver vs. customer responsibilities)
- Cost comparison vs. traditional development to justify value

**Pricing rules:**
- Phase 1 (Contracted): Fixed price, binding commitment. 30% start + 40% midpoint + 30% go-live
- Phase 2 (Conditional): Estimate only, initiated when Phase 1 generates revenue. Funded from platform profit.
- Phase 3 (Roadmap): Not quoted. Strategic direction only.
- Total = Contracted + Conditional shown prominently

**Output:** `plan/PROPOSAL.html`
See `references/templates/proposal-html-template.md`

## Pricing Formula

```
Contracted MVP Price = (Dev Hours × Hourly Rate × Complexity Factor) + Infrastructure Margin + Risk Buffer
```

Default assumptions (adjust per project):
- Hourly rate: $80–150/hr depending on tech stack and team seniority
- Complexity factor: 1.0–1.3 based on integration surface area
- Infrastructure margin: 5–10% of total
- Risk buffer: 10–15% of base estimate

Round to clean numbers (nearest $5K or $10K for client readability).

## Iterative Refinement

After delivering draft HTML:
- Client feedback comes in specific format tweaks
- Each fix goes to the specific HTML file; avoid regressions
- Common fixes: color contrast, layout adjustments, note visibility, cost buffers
- Update HTML version number on every change
- Log changes in daily memory

## Skill Execution

When invoked for a new project:
1. Gather requirements from conversation context or ask clarifying questions
2. Create project directory at `~/projects/<project>/plan/`
3. Execute Stages 1–5 sequentially
4. After each stage, present output for review before proceeding (unless user says "go ahead")
5. Final output: `PROPOSAL.html` is the signable artifact

## Reference Implementation

Canonical example: `~/projects/ely/plan/` — ELY Wellness Platform
- 19 modules across 3 phases
- $45K contracted + $38K conditional = $83K total
- Full traceability from Draft scope → Module plans → Master Summary → Project Plan → Proposal
