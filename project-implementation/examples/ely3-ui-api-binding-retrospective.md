# ELY3 Retrospective — UI Looks Integrated, APIs Are Not

**Date:** 2026-06-12

## Symptom

Customer reported: UI close to mockups after fidelity sprint, but buttons, links, and metrics do not reflect backend data. "How did integration tests pass?"

## Answer

| Test | Layer | Proves |
|------|-------|--------|
| `npm run test:integration` | Express handlers via supertest | API contracts |
| `npm run test:ui` | `App.jsx` string match | Routes exist |
| `npm run uat:browser` | Playwright smoke | Page loads, text present |
| None of the above | React `apiClient` calls per widget | UI–API binding |

## Examples found (ELY3)

- Loyalty points `1,250` — hardcoded (no E08 API wired)
- Notification badge `2` — hardcoded
- Profile progress `60%` — hardcoded
- Therapist Export — visible, no API
- Admin user count — wrong query (`limit=1` without total)

## Fidelity sprint made it worse

Visual completeness pressure led agents to **fill mockup slots with static data** — the opposite of integration.

## Fix encoded in skills

1. Split UI gate: **Functional / Fidelity / API Binding** (N.3b / N.3c / N.3d)
2. New artifact: `plan/UI_API_BINDING_MANIFEST.md`
3. New script: `npm run test:ui-bindings`
4. `npm run test:all` includes `test:frontend` (ui + bindings)
5. Exit to UAT requires Binding ✅, not just API green

## Lesson

> **Backend Done ≠ Product Done.**  
> **UI Pretty ≠ UI Integrated.**
