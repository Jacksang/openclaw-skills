# Project Quoter — Methodology Reference

## Overview

The Project Quoter methodology transforms raw project requirements into a complete, client-ready proposal with phased pricing, effort estimates, and a professional HTML quote document. It was developed and battle-tested on the **ELY Wellness Platform** project (2026-05), which produced 19 modular plans, 83 technical tasks, 99 user stories, 188 behavior tests, and a $83K SGD proposal across 3 phases.

## Design Philosophy

### Why This Works

1. **Traceability from scope to price.** Every dollar in the proposal traces back to specific technical tasks with estimated hours. No black-box pricing.
2. **Phase gating reduces risk for both sides.** Client only commits to MVP. Growth phases are funded from platform revenue — shared risk.
3. **Self-contained HTML outputs.** No dependency on presentation software. Client opens a browser and sees the full picture.
4. **Modular architecture thinking applied to planning.** Each module is independently buildable, testable, and estimable.

### When to Use

- Software project with moderate-to-high complexity (5+ distinct modules)
- Client needs formal proposal for budget approval
- Multi-phase delivery with clear MVP/future boundaries
- Budget range: $20K–$200K SGD (methodology scales)
- Client values transparency and traceability in pricing

### When NOT to Use

- Trivial projects (<3 modules, <$10K) — overkill
- Fixed-scope waterfall projects where client wants one single number
- Projects where the client already has a detailed RFP with fixed budget

## The Document Pipeline

The full stage list (0–8, including commercial inputs, estimation, pricing, and negotiation) is defined in `SKILL.md`. The document flow is:

```
Planner inputs → Modular Breakdown → Master Aggregation → Project Plan HTML → Proposal HTML
    ↓                ↓                    ↓                     ↓                  ↓
 Draft scope.md   E01–E0N plans    MASTER_SUMMARY.md    PROJECT_PLAN.html   PROPOSAL.html
 (from planner)   (engineering)    (INTERNAL ONLY)      (client overview)   (client signing)
```

### Stage Transitions

- **1→2:** Only when scope boundaries are clear and commitment levels are set
- **2→3:** Only when all modules have estimates and dependencies mapped
- **3→4:** Only when totals are verified against individual module sums
- **4→5:** Only when project plan accurately reflects module-level data

## Key Decision Points

### Commitment Level Assignment

| Level | Criteria | Phase |
|-------|----------|-------|
| **Contracted** | Core business value, clearly defined, no external API dependencies blocking delivery | Phase 1 |
| **Conditional** | Adds significant value but requires Phase 1 validation; may depend on external approvals | Phase 2 |
| **Roadmap** | Strategic vision; depends on unvalidated assumptions, data availability, or legal review | Phase 3 |

### What Goes in Phase 1 (Contracted MVP)

Ask: "Can the business launch without this?"
- If NO → Phase 1, Contracted
- If "Launch is possible but painful" → Phase 1, Conditional (good-to-have)
- If "Nice after launch" → Phase 2
- If "This needs data/users first" → Phase 3

### Estimation Verification

Before locking estimates:
1. Cross-check: Does each module estimate feel proportional to its scope?
2. Sanity check: Total hours ÷ hourly rate ≈ comparable projects?
3. Risk check: Is risk buffer explicit and itemized, not just "add 20%"?
4. Client check: Will the client feel this is fair and transparent?

## Pricing Strategy

### Fixed Price (Phase 1)
- Binding commitment with clear scope boundaries
- Payment milestones aligned with deliverables
- Includes risk buffer for unknowns within defined scope

### Conditional Estimate (Phase 2)
- Not binding — initiated only when conditions are met
- Priced as estimate range with noted assumptions
- Reduces client risk; they only pay for Phase 2 from proven revenue

### Not Quoted (Phase 3)
- Avoids anchoring client expectations prematurely
- Keeps proposal focused on what's actually committed
- Leaves room for strategic partnership discussion

## Common Pitfalls & Fixes

### Pitfall 1: Underquoting Infrastructure
**Symptom:** Client says "AWS costs more than your estimate"
**Fix:** Add 2–3× buffer on infra estimates. Better to say "$20–40/month" and have client spend $25 than say "$5–15" and have them spend $35.

### Pitfall 2: Vague Commitment Boundaries
**Symptom:** Client assumes Phase 2 features are included in Phase 1 price
**Fix:** Use explicit commitment labels (Contracted/Conditional/Roadmap) with distinct visual styling. Repeat in multiple sections.

### Pitfall 3: Missing Customer Responsibilities
**Symptom:** Dispute after contract signing about who pays for domain/SSL/WhatsApp fees
**Fix:** Dedicated "Included vs. Excluded" section with two-column layout. List every third-party cost explicitly.

### Pitfall 4: Total Row CSS Conflicts
**Symptom:** Total row text invisible because global CSS overrides inline styles
**Fix:** Apply background colors to individual `td` elements, not the `tr`. Use `!important` if needed, or better, restructure CSS to avoid the conflict.

### Pitfall 5: Qualification Notes Lost in Bullets
**Symptom:** Important caveats buried in bullet lists that clients skip
**Fix:** Pull qualification notes into separate bordered callout boxes with distinct background and left-border accent.

### Pitfall 6: Pricing Without Traceability
**Symptom:** Client asks "Why $45K?" and you can't break it down
**Fix:** Every price traces to module → task → hours. The MASTER_SUMMARY.md is your audit trail.

## Iteration Protocol

After delivering draft HTML to client:
1. Client identifies issues (format, clarity, cost)
2. Fix the specific HTML file; do NOT regenerate from scratch (would lose prior fixes)
3. Increment version number
4. Verify fix with client screenshot or description
5. Log the iteration in the project changelog

## Reference Implementation Checklist

When starting a new project, verify against ELY:

- [ ] Draft scope categorizes all features by phase and commitment level
- [ ] Each module has: user stories + technical tasks + test cases + estimates + dependencies
- [ ] Master summary has: aggregate hours + calendar timeline + team composition + risks + infra costs
- [ ] Project plan HTML has: summary cards + timeline bar + module tables + Gantt + team table
- [ ] Proposal HTML has: cover + exec summary + workflow + outcomes + commitment table + scope + timeline + methodology + payment + inclusions/exclusions + cost comparison
