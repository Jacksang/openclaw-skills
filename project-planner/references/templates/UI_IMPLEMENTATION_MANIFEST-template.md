# UI Implementation Manifest Template

Copy to `plan/UI_IMPLEMENTATION_MANIFEST.md` at planner Stage 7 (after `ARCHITECTURE.md`). Implementation imports this into SCRUM_BOARD — **without it, frontend work will not be dispatched.**

## Structure

```markdown
# UI Implementation Manifest

> **Generated:** YYYY-MM-DD | **Source:** uidesign/INDEX.md + contracted Phase 1 scope
> **Rule:** One ✅ row required per PAGE before project-implementation exit gate.

| Task ID | PAGE_ID | Route | Wireframe | Epic | Mockup | Functional | Fidelity | API Binding | Notes |
|---------|---------|-------|-----------|------|--------|:----------:|:--------:|:-------------:|-------|
| UI-E01-01 | PAGE_001 | #/login | uidesign/PAGE_LOGIN.md | E01 | mockup-login.png | ⬜ | ⬜ | ⬜ | |
| UI-E02-01 | PAGE_020 | #/therapist/sessions | uidesign/PAGE_THERAPIST_SESSIONS.md | E02 | mockup-therapist-sessions.png | ⬜ | ⬜ | ⬜ | |

## Deferred (no wireframe yet)
| PAGE_ID | Reason |
|---------|--------|
| PAGE_099 | Phase 2 — not contracted |

## Three UI Gates (do not conflate)

| Gate | Checklist |
|------|-----------|
| **Functional** | Route, role guard, loading/empty/error/success, `npm run test:ui` |
| **Fidelity** | Wireframe + mockup screenshot compare, design tokens |
| **API Binding** | Every widget in `UI_API_BINDING_MANIFEST.md` ✅ or ⏸️ hidden |

Also generate **`plan/UI_API_BINDING_MANIFEST.md`** (see `UI_API_BINDING_MANIFEST-template.md`).
```

## Rules

- **Task ID** format: `UI-E0X-NN` — maps to SCRUM_BOARD `C[N]-UI-NN`
- Include every **contracted** PAGE from `uidesign/INDEX.md` that has a `PAGE_*.md` wireframe
- **Functional / Fidelity / API Binding:** ⬜ · 🔵 · ✅ per column (all three required for PAGE Done)
- Shared components from `COMPONENTS_SHARED.md` → note in Notes column or separate rows if they need implementation tasks
- Regenerate manifest when design approval adds/removes pages; bump version in header
