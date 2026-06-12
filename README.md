# OpenClaw Skills — Custom Collection

Custom-developed AI agent skills for [OpenClaw](https://openclaw.ai), built and maintained by Eva2. Also installable as Cursor skills.

## The Project Delivery Chain

Six skills form a single pipeline that takes a market domain from research to production go-live:

```
market-research-agent → project-planner → project-quoter → ⏸️ CUSTOMER SIGNS → project-implementation → project-uat → project-delivery
```

| Skill | Stage | Key Outputs |
|-------|-------|-------------|
| **market-research-agent** | Domain analysis (history/present/future trends) → scored software/hardware opportunities → solution specs → planner-ready requirement scopes. Optional entry point. | `research/<domain>/MARKET_ANALYSIS.md`, `OPPORTUNITIES.md`, `SOLUTION-S0X-*.md`, per-solution `Draft scope.md` |
| **project-planner** | Requirements capture → stories → tests → UI/UX → **Docker local-first architecture** (AWS optional for cloud delivery). Epic-by-epic validation. | `Draft scope.md`, `E0X-*-stories.md`, `tests/`, `uidesign/`, `ARCHITECTURE.md`, `GLOSSARY.md`, `STATUS.md` |
| **project-quoter** | Dual-track estimation: **gate-based human** @ $250 SGD/hr + **AI wall-clock** @ $9.50 SGD/hr → tiered pricing → PNG timelines → client proposal. | `MASTER_SUMMARY.md` (internal), `plan/assets/*.png`, `PROJECT_PLAN.html`, `PROPOSAL.html` |
| **project-implementation** | Docker Compose local env → Phase 0 test infra → phased coding (TDD workers) → integration gates. | `docker-compose.yml`, `SCRUM_BOARD.md`, `TEST_REPORT_E0X.md` |
| **project-uat** | UAT against **local Docker stack** (from implementation) → validation → AI + human rounds → sign-off → hand off to delivery. | `tests/uat/`, `UAT_SIGNOFF.md` |
| **project-delivery** | **Local Docker delivery first** → customer sign-off → optional cloud go-live after written approval. | `LOCAL_DELIVERY_RUNBOOK.md`, `LOCAL_SETUP.md`, `SECURITY_CHECKLIST.md` |

Each skill verifies its upstream inputs (entry gate) and produces a defined handoff (exit gate / deliverables contract). Estimation accuracy compounds across projects via `project-quoter/references/calibration-log.md`. Mid-project scope changes follow a shared rule: defects are fixed free, new scope becomes a change order routed through a delta-epic mini-pipeline (the epic is the unit of change — never silently absorbed, never a full re-run).

## Standalone Skills

| Skill | Status | Description |
|-------|--------|-------------|
| **tdd-workflow** | ✅ Active | Test-driven development: generate tests first, stub, then implement to pass all tests |
| **path-resolution-issues** | 📝 Notes | Diagnostic notes on skill path resolution |

## Installation

### OpenClaw

```bash
cp -r <skill-name> ~/.npm-global/lib/node_modules/openclaw/skills/
```

Or install via ClawHub when published.

### Cursor

```bash
cp -r <skill-name> ~/.cursor/skills/
```

The OpenClaw-specific frontmatter fields (`allowed-tools`, `user-invocable`) are ignored by Cursor; the same files work in both systems.

## Development

These skills live at `~/.openclaw/workspace/skills/` and are copied to the installation directory for use.

To contribute a new skill:
1. Develop in `~/.openclaw/workspace/skills/<skill-name>/`
2. Follow the [Skill Creator](https://docs.openclaw.ai) guidelines: frontmatter with `name` + `description` (third person, with "Use when…" triggers), SKILL.md under 500 lines, detailed material in `references/`/`templates/`/`examples/`
3. Copy to the skills directory
4. Commit and push to this repo
