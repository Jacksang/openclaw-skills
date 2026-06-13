# UI–API Binding Manifest Template

Copy to `plan/UI_API_BINDING_MANIFEST.md` at planner **Stage 7b** (after `UI_IMPLEMENTATION_MANIFEST.md`).

## Purpose

Maps every **visible widget and interactive control** on each PAGE to a backend endpoint. Prevents "pretty UI with fake data" from passing as Done when API integration tests are green.

## Structure

```markdown
# UI–API Binding Manifest

> Fidelity ✅ without Binding ✅ is NOT shippable.

## PAGE_010 — Customer Dashboard

| Widget / Action | API | Status | Notes |
|-----------------|-----|--------|-------|
| Next session card | GET /api/v1/sessions | ⬜ | |
| Loyalty points | GET /api/v1/loyalty/balance | ⬜ | E08 — hide if deferred |

## Gate
- [ ] All rows ✅ or ⏸️ Deferred (widget hidden + waiver)
- [ ] `npm run test:ui-bindings` passes
```

## Rules

- Derive rows from `tests/E0X-*-tests.md` **UI Behavior** sections and `uidesign/PAGE_*.md` wireframes
- **⏸️ Deferred** only when no API exists in contracted scope — widget must be **hidden**, not faked
- Implementation updates Status columns during N.3d gate
- One combined gate summary at bottom: `Binding complete: X/Y pages`
