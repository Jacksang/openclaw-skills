# ELY3 UI Gap Retrospective

**Date:** 2026-06-12 | **Lesson:** Planning UI ≠ Implementing UI without traceability gates

## Symptom

- `uidesign/` complete (8 P1 pages, mockups, design system)
- APIs + integration tests 49/49 pass
- Frontend: 3 placeholder routes (~0/9 manifest pages)
- UAT Round 1 claimed pass without browser design review

## Root Cause

| Layer | Failure |
|-------|---------|
| Planner → implementer | No `UI_IMPLEMENTATION_MANIFEST.md` |
| Module plans | API tasks only (E01-T1…T5) |
| Behavior tests | API-only; tester example reinforced supertest |
| SCRUM_BOARD | No C*-UI-* tasks |
| Phase Done | `npm run test:all` = API only |
| Exit gate | "Frontend runs" = Docker web returns 200 |
| UAT | Downstream; could not pull UI work upstream |

## Fix Applied to Skill Chain

1. **project-planner:** UI manifest at Stage 7; split API/UI behavior tests
2. **project-implementation:** Dual-track phases, frontend worker template, UI gate N.3b, manifest import Phase 0.8
3. **project-uat:** Block entry if manifest incomplete

## Rule for Coordinators

> **Frontend Done = UI matches uidesign + APIs integrated** — same weight as integration test pass.

See `references/PROCESS_GAP_ANALYSIS.md` (copy to project `plan/` for post-mortems).
