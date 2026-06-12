# UI Implementation Manifest Template

Copy to `plan/UI_IMPLEMENTATION_MANIFEST.md` at planner Stage 7 (after `ARCHITECTURE.md`). Implementation imports this into SCRUM_BOARD — **without it, frontend work will not be dispatched.**

## Structure

```markdown
# UI Implementation Manifest

> **Generated:** YYYY-MM-DD | **Source:** uidesign/INDEX.md + contracted Phase 1 scope
> **Rule:** One ✅ row required per PAGE before project-implementation exit gate.

| Task ID | PAGE_ID | Route | Wireframe | Epic | Mockup | Status | Notes |
|---------|---------|-------|-----------|------|--------|--------|-------|
| UI-E01-01 | PAGE_001 | #/login | uidesign/PAGE_LOGIN.md | E01 | uidesign/assets/login.png | ⬜ | |
| UI-E01-02 | PAGE_002 | #/admin/dashboard | uidesign/PAGE_ADMIN_DASH.md | E01 | — | ⬜ | |
| UI-E02-01 | PAGE_020 | #/sessions | uidesign/PAGE_SESSIONS.md | E02 | uidesign/assets/sessions.png | ⬜ | |

## Deferred (no wireframe yet)
| PAGE_ID | Reason |
|---------|--------|
| PAGE_099 | Phase 2 — not contracted |

## Per-PAGE UI Gate Checklist (copy to TEST_REPORT_E0X-UI.md)

For each PAGE when marking ✅:
- [ ] Route exists (matches INDEX.md)
- [ ] Role guard correct
- [ ] Layout matches wireframe (desktop; mobile if spec requires)
- [ ] States: loading, empty, error, success
- [ ] Wired to live API (not demo-only hardcoded data)
- [ ] Design system tokens; no emoji; no unapproved third-party scripts
- [ ] `npm run test:ui` smoke passes for this PAGE
```

## Rules

- **Task ID** format: `UI-E0X-NN` — maps to SCRUM_BOARD `C[N]-UI-NN`
- Include every **contracted** PAGE from `uidesign/INDEX.md` that has a `PAGE_*.md` wireframe
- **Status:** ⬜ Pending · 🔵 In Progress · ✅ Done (UI gate passed)
- Shared components from `COMPONENTS_SHARED.md` → note in Notes column or separate rows if they need implementation tasks
- Regenerate manifest when design approval adds/removes pages; bump version in header
