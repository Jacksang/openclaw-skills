---
name: market-research-agent
description: "Research a market domain end-to-end: history, present state, and future trends; then derive software/hardware solution opportunities, specify each solution, and generate a Draft scope.md ready for the project delivery pipeline. Use when the user asks for market research, domain analysis, industry trends, competitor analysis, opportunity discovery, or wants solution ideas and requirement scopes for a market."
allowed-tools: read,write,edit,exec,web_search
user-invocable: true
---

# Market Research Agent

From a market domain to actionable build decisions: a comprehensive past/present/future analysis of the domain, a prioritized list of software/hardware solution opportunities, and a requirements scope per solution that feeds directly into the project delivery pipeline.

## Position in Skill Chain

```
market-research-agent → project-planner → project-quoter → ⏸️ SIGN → implementation → uat → delivery
```

Each generated `Draft scope.md` is a valid Stage 1 input for **project-planner**. The market analysis also feeds **project-quoter**'s value-based and market-benchmark pricing levers.

## Research Quality Rules (Apply to Every Stage)

1. **Use live web search for all market figures, competitor facts, and trend claims.** Training data is stale by definition — never present remembered numbers as current.
2. **Cite every figure**: source name, URL, publication year. No naked numbers.
3. **Label every claim**: `[FACT — source]`, `[ESTIMATE — reasoning]`, or `[ASSUMPTION — needs validation]`.
4. **Triangulate market sizes** from ≥ 2 independent sources; if they disagree, show the range and say why.
5. **Flag primary-research needs**: where only customer interviews or paid reports can answer, say so explicitly.

## File Layout

All outputs under `research/<domain>/`:

```
research/<domain>/
├── BRIEF.md                  Stage 0 — research brief
├── MARKET_ANALYSIS.md        Stage 1 — past / present / future
├── OPPORTUNITIES.md          Stage 2 — scored solution candidates
├── SOLUTION-S01-[name].md    Stage 3 — one spec per selected solution
└── S01-[name]/
    └── Draft scope.md        Stage 4 — planner-ready requirements scope
```

---

## Stage 0: Research Brief

Ask the user before researching (do not assume):
- **Domain** and geography (global? regional?)
- **Intent**: explore the market / validate an idea / find something to build
- **The user's position**: outsider, existing player, vendor to the industry
- **Constraints**: budget class, software-only vs. open to hardware, timeline

**Output:** `BRIEF.md` — 10 lines max. Confirm with the user, then proceed.

---

## Stage 1: Domain Analysis (Past → Present → Future)

**Output:** `MARKET_ANALYSIS.md` with exactly these sections:

### 1. History & Evolution
- Origin and key eras of the domain (what changed, when, and why)
- Technology shifts that reshaped it; players that rose/fell with each shift
- Regulatory or economic events that redefined the market
- Lesson extraction: what does this history predict about the next shift?

### 2. Present State
- **Market definition & size**: TAM / SAM / SOM with reasoning and cited sources
- **Value chain**: who produces, distributes, services, and captures margin
- **Competitive landscape**: direct + indirect competitors; matrix comparing price, features, market position, strengths, weaknesses
- **Customer segments**: personas, top 5 pain points, buying criteria, willingness to pay
- **Current technology stack** of the domain: what tools/systems incumbents actually use today, and their age
- **Regulation & standards** currently in force

### 3. Future Trends (3–5 Year Outlook)
- 5–7 trends with evidence, each rated: impact (H/M/L) × confidence (H/M/L)
- Growth drivers and barriers
- Disruption candidates: technologies or entrants that could break the current structure
- Scenario sketch: base case / accelerated case / stagnation case

### 4. Strategic Summary
- SWOT for a new entrant (from the user's position in the brief)
- Porter's Five Forces, one paragraph each
- Go-to-market notes: channels, common pricing models, sales cycle expectations, key partnerships

---

## Stage 2: Opportunity Identification

**Goal:** Convert pain points and market gaps into concrete solution candidates.

**Process:**
1. Cross-reference Stage 1's pain points × competitive gaps × trends → list candidate solutions.
2. Classify each: **Software** / **Hardware** / **Hybrid** (hardware + companion software).
3. Score each candidate 1–5 on: pain severity, market size, competitive whitespace, technical feasibility, time to market, fit with user's constraints.
4. Rank by total score.

**Output:** `OPPORTUNITIES.md`:

```markdown
| ID | Solution | Type | Pain | Market | Whitespace | Feasibility | Speed | Fit | Total |
|----|----------|------|------|--------|------------|-------------|-------|-----|-------|
| S01 | ... | Software | 5 | 4 | 3 | 5 | 4 | 5 | 26 |
```

Each candidate gets a half-page: the pain it solves, who pays, why now (trend link), and the main risk.

**⏸️ Checkpoint:** Present the ranked list to the user. They select which solutions proceed to Stage 3 (typically 1–3).

---

## Stage 3: Solution Specification (Per Selected Solution)

**Output:** `SOLUTION-S0X-[name].md` per solution:

### Concept
- Value proposition in one sentence; target user roles (these become the planner's personas)
- Business model: who pays, how much, how often (feeds the quoter's value-based pricing)

### Software Specification
- Core capabilities (the epics-to-be), platform (web / mobile / embedded / desktop)
- Key integrations and data sources
- Non-functional expectations: scale, availability, security/compliance obligations from Stage 1's regulation section

### Hardware Specification (Hybrid/Hardware only)
- Device concept: sensors, compute class, connectivity, power, enclosure environment
- Certification requirements (CE/FCC/medical/etc. per the domain's regulations)
- BOM cost class and indicative lead times
- **Boundary note:** firmware and companion software flow through the project delivery pipeline; hardware design, certification, and manufacturing are a separate procurement track — flag this split explicitly so the later quote doesn't silently absorb it

### Validation & Risks
- Riskiest assumption and the cheapest experiment to test it
- Competitor reaction expected; regulatory exposure

---

## Stage 4: Requirements Scope Generation (Pipeline Handoff)

**Goal:** For each specified solution, produce a `Draft scope.md` that project-planner accepts as its Stage 1 input without rework.

**Process:**
1. Use the exact structure from project-quoter's `references/templates/draft-scope-template.md`: features grouped into Phase 1 (Contracted) / Phase 2 (Growth) / Phase 3 (Roadmap) with commitment levels.
2. Each feature gets: Business Objective, Functional Requirements, Non-Functional Requirements, Technical Implementation Notes, Acceptance Criteria, Dependencies & Risks.
3. Derive Phase 1 from the solution's core capabilities ("can the business launch without this?"); push trend-bets to Phase 2/3 with explicit KPI gates.
4. For hybrid solutions, scope only the software/firmware in the phases; list hardware procurement as an external dependency.

**Output:** `research/<domain>/S0X-[name]/Draft scope.md`

**⏸️ Handoff:** Tell the user each scope is ready to enter the pipeline: copy it to the new project's `plan/` folder and invoke **project-planner**.

---

## Stage 5: Final Recommendation

One page, in chat and appended to `OPPORTUNITIES.md`:
- Which solution to pursue first and why (evidence-linked, not vibes)
- What to validate before committing build budget
- Suggested sequencing if multiple solutions were scoped

---

## Antipatterns

| Antipattern | Fix |
|-------------|-----|
| Quoting market sizes from memory | Web-search and cite; triangulate ≥ 2 sources |
| Analysis with no artifact | Every stage writes its file under `research/<domain>/` |
| Solutions disconnected from evidence | Every candidate links to a pain point + gap + trend from Stage 1 |
| Scope format the planner can't ingest | Use the draft-scope template verbatim |
| Hiding hardware costs inside a software scope | Hardware is a flagged external track with its own costs |
| Skipping the Stage 2 checkpoint | The user picks what gets specified — not the agent |
