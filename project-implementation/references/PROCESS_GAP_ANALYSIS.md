# Process Gap Analysis — UI Planned but Not Implemented

Copy to `plan/PROCESS_GAP_ANALYSIS.md` when a project hits this pattern. Reference for coordinators and post-mortems.

## Symptom

- `uidesign/` complete: wireframes, design system, mockups, `INDEX.md`
- Backend APIs + integration tests pass (often 100%)
- Frontend: placeholder routes or a single shell page — most `PAGE_*` designs not built
- UAT may run API-only or claim pass without browser design review
- Customer discovers missing UI at demo or UAT Round 2

## Root Cause (Process, Not Agent Malice)

Agents optimize for **what is task-listed, automated, and gate-enforced**. UI was designed in planning but **never entered the execution loop**.

| Layer | What existed | What was missing | Effect |
|-------|----------------|------------------|--------|
| **Planner output** | `uidesign/PAGE_*.md`, stories | `UI_IMPLEMENTATION_MANIFEST.md` | No countable PAGE backlog for implementers |
| **Behavior tests** | `tests/E0X-*-tests.md` | API/UI split; UI sections | Tester + workers default to supertest only |
| **Module / SCRUM plans** | API tasks (E01-T1…) | `C*-UI-*` frontend tasks | Coordinators never dispatch UI work |
| **Worker prompts** | Backend TDD template | Frontend worker with wireframe DoD | Agents implement APIs only |
| **Phase Done criteria** | `npm run test:all` green | UI gate per PAGE | API green = phase Done |
| **Exit gate** | "App runs" | All manifest rows ✅ | Docker returns 200 on one route |
| **UAT** | UI checklist in pass criteria | Block if manifest incomplete | UAT cannot pull UI work upstream |

**Insight:** Integration tests and UAT **verify** behavior; they do **not** **create** frontend tasks. Without manifest → board → frontend worker → UI gate, UI stays optional in practice.

## Why Gates Failed to Catch It

1. **Integration tests** exercise HTTP — not layout, routes per role, or wireframe states.
2. **"Build succeeds"** — compiles ≠ pages exist.
3. **"Frontend runs"** — one Vite/React shell satisfies smoke; 8 pages do not.
4. **UAT cases** written after the fact may use Postman workarounds if pages are missing.
5. **Entry gates listed uidesign** but nothing forced **per-PAGE completion** before handoff.

## Fix (Skill Chain — Applied)

| Skill | Fix |
|-------|-----|
| **project-planner** | Stage 4: split API vs UI behavior tests; Stage 7: `UI_IMPLEMENTATION_MANIFEST.md` |
| **project-implementation** | Dual-track phases; Phase 0.8 manifest → SCRUM_BOARD; frontend worker; UI gate N.3b; exit gate manifest ✅ |
| **project-uat** | Block entry if manifest incomplete — return to implementation |
| **tester-agent-prompt** | API track + UI track both required for Done |

## Coordinator Rule

> **Frontend Done = UI matches `uidesign/` + APIs integrated** — same weight as integration test pass.

Not: API Done + placeholder HTML.  
Not: UAT will catch it later.

## Detection Checklist (Run Mid-Implementation)

- [ ] `plan/UI_IMPLEMENTATION_MANIFEST.md` exists with one row per contracted PAGE
- [ ] SCRUM_BOARD has `C*-UI-*` tasks linked to manifest rows
- [ ] `tests/E0X-*` files have `### 🖥️ UI Behavior Tests` sections
- [ ] Phase Done requires UI gate (N.3b), not only `npm run test:integration`
- [ ] Midpoint demo is **browser walkthrough** of new PAGE(s), not API-only

If any fail, stop backend work and fix process before continuing.

## Reference

See `project-implementation/examples/ely3-ui-gap-retrospective.md` for a real project instance.
