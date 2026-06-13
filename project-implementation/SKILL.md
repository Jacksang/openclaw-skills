---
name: project-implementation
description: "Implementation & testing workflow: Phase 0 test infrastructure, phased feature coding with worker agent dispatch, unit test validation, integration test gates, and bug fix cycles. Use when starting to build a planned software project, implementing features from user stories, or coordinating coding and tester agents after planning and quoting are complete."
allowed-tools: read,write,edit,exec
user-invocable: true
---

# Implementation & Testing Workflow

A battle-tested workflow for taking a project from planning (user stories, test plans, UI designs) through implementation with integrated testing at every phase.

## Position in Skill Chain

```
project-planner → project-quoter → ⏸️ CUSTOMER DECISION → project-implementation → project-uat → project-delivery
```

## Entry Gate (Verify Before Starting)

- [ ] **Signed proposal exists** (quoter decision gate). Never start implementation without it — confirm with the user if uncertain.
- [ ] User stories with acceptance criteria (`plan/E0X-[role]-stories.md`)
- [ ] Behavior test plans (`tests/E0X-[role]-tests.md`)
- [ ] UI design documents (`uidesign/*.md`) and customer design approval (`plan/DESIGN_APPROVAL.md`) — **do not start if approval is pending unless the customer explicitly waives in writing**
- [ ] **`plan/UI_IMPLEMENTATION_MANIFEST.md`** — one row per contracted PAGE with task IDs (from planner Stage 7; see project-planner skill)
- [ ] Architecture decisions (`plan/ARCHITECTURE.md` — tech stack, schema, API conventions, security requirements, NFR targets)

If any input is missing, run the upstream skill first.

**Risk tracking:** copy the risk items from the quoter's module plans into `plan/RISKS.md` (risk, probability, impact, mitigation, status). Review it at every phase gate — risks that were priced must be actively managed, not forgotten.

**Stack note:** templates in this skill assume Node.js/TypeScript (`npm`, `node:test`, `supertest`). Adapt commands and libraries to whatever `plan/ARCHITECTURE.md` specifies.

## Core Principles

1. **Test infrastructure is Phase 0** — before any feature code
2. **Every module ships with unit tests** — no exceptions
3. **Integration testing gates every phase** — tester agent runs after coding
4. **UI is not optional** — every `PAGE_*` in `plan/UI_IMPLEMENTATION_MANIFEST.md` for contracted epics must pass **three UI gates** (functional, fidelity, API binding) before the epic is Done
5. **Dual-track delivery** — each phase has **backend** and **frontend** tracks; both must pass their gates
6. **API integration tests ≠ UI integrated** — supertest proves HTTP contracts; it never proves React calls those endpoints. Binding requires `plan/UI_API_BINDING_MANIFEST.md` + `npm run test:ui-bindings` + Playwright actions
7. **Bugs are found and fixed in the same sprint**
8. **One-at-a-time worker dispatch** — avoid lock contention
9. **Only the coordinator pushes** — after **`npm run test:all` AND all three UI gates** pass locally on the Docker stack; never push untested commits to GitHub
10. **Integration tests have hard timeouts** — hung tests must fail fast locally, not stall CI for minutes

These rules encode real retrospectives — see `examples/ely2-retrospective.md` (API gates) and the project's `plan/PROCESS_GAP_ANALYSIS.md` (UI traceability).

### Why UI Gets Omitted Without Dual-Track Gates

Planning produces `uidesign/PAGE_*.md` but behavior tests and worker prompts default to **API/supertest**. Agents complete what is **task-listed, automated, and gate-enforced**. If SCRUM_BOARD has `C2-01 Session API` but no `C2-UI-01 PAGE_020`, and phase Done = `npm run test:all`, the frontend will be skipped even though entry gates list uidesign. **Fix: manifest + frontend worker + UI gate per phase — not UAT alone.**

---

## Phase Structure

```
Phase 0: Test Infrastructure
Phase N: Feature Phases (repeatable) — DUAL TRACK
  ├── N.1a Backend coding → N.2a Unit tests → N.3a API integration tests
  ├── N.1b Frontend coding → N.2b UI smoke tests → N.3b/N.3c/N.3d UI gates (see below)
  └── N.4 Bug fix cycle (both tracks)

UI gates (all three required — do not conflate):
  N.3b Functional — routes, role guards, loading/empty/error/success states
  N.3c Fidelity — layout matches uidesign/ + mockup screenshot comparison
  N.3d API Binding — every display + action wired to live API per UI_API_BINDING_MANIFEST
```

---

## Phase 0: Test Infrastructure (MANDATORY)

**Before any feature code is written:**

### 0.1 Local Environment (Docker — Mandatory)

Per `plan/ARCHITECTURE.md` local-first strategy:

- Create `docker-compose.yml` — app, database, and supporting services
- Add `.env.example`; document ports and volume mounts in `docs/LOCAL_SETUP.md`
- Verify `docker compose up` starts cleanly; migrations run; health endpoint responds
- All development, integration tests, and phase demos run against this stack — **not AWS**

### 0.2 Database Setup
- Verify database is running (via Docker Compose)
- Set up `.env` with correct connection string and port
- Run all migrations — verify they complete cleanly
- Fix any migration issues (missing `IF NOT EXISTS`, wrong paths, ownership)

### 0.3 Unit Test Runner
- Install test dependencies (`jest`, `vitest`, or `node:test`)
- Create `npm test` script that runs all `__tests__/` directories
- Verify a smoke test passes (e.g., `1+1=2`)
- Add `--test-concurrency=1` if tests share database state

### 0.4 Integration Test Runner
- Install supertest (or equivalent HTTP test library)
- Create `npm run test:integration` with **timeouts enforced** (see package.json template below)
- Create `tests/integration/setup.ts` with:
  - Supertest client pointed at the app
  - **HTTP request timeout** on every call (default **10s** — fail fast on hung endpoints)
  - Auth helpers (`loginAs(role)`, `authApi(role)`)
  - Database reset helper (full cascade cleanup)
- **Per-test timeout:** default **30s** via `--test-timeout=30000`; override per `it()` only when justified (e.g. large upload, max **60s**)
- **Suite budget:** full integration suite should finish in **< 10 minutes** locally — if longer, fix hanging tests or split suites by epic
- Verify the app starts and health endpoint responds

### 0.5 Test Plan Inventory
- Catalog all test plan files (`tests/E0X-*.md`)
- Map each to the phase that owns it
- Add test tasks (T1A, T1B, etc.) to `SCRUM_BOARD.md` (see template below)

### 0.6 Tester Agent Prompt
- Copy `templates/tester-agent-prompt.md` (from this skill) to the project's `agent-prompts/tester.md`
- Replace `MODEL_NAME` with a fast/cheap model (e.g. a flash-class model) — premium models are wasted on test execution
- It includes: test methodology, bug format, report format, quality gates

### 0.8 UI Implementation Manifest → SCRUM_BOARD (MANDATORY)

Import `plan/UI_IMPLEMENTATION_MANIFEST.md` into `SCRUM_BOARD.md`:

- One **frontend coding task** per manifest row: `C[N]-UI-XX` → `PAGE_XXX` + wireframe path
- One **UI test task** per epic: `T[N]-UI` → PAGE checklist + `npm run test:ui` (or Playwright smoke)
- Do **not** start epic N backend until manifest rows for epic N are on the board

If manifest is missing, run planner Stage 7 supplement (generate from `uidesign/INDEX.md`) before coding.

### 0.8b UI–API Binding Manifest (MANDATORY)

Copy `project-planner` template to **`plan/UI_API_BINDING_MANIFEST.md`**:

- One row per **widget, metric, button, and link** on each contracted PAGE
- Columns: widget/action, HTTP method + path, status (✅/⚠️/❌/⏸️ Deferred)
- Generated from user stories + `tests/E0X-*-tests.md` **UI Behavior** sections
- Add `phase1/web/scripts/verify-api-bindings.mjs` (or stack equivalent) in Phase 0
- `npm run test:ui-bindings` must pass before marking **API Binding** ✅

**Hard rule:** Fidelity sprint must not add hardcoded demo metrics to "look complete" (loyalty points, notification counts, fake progress bars). Wire API or mark ⏸️ Deferred and hide the widget.

### 0.9 CI/CD Pipeline (if applicable)
- Copy `templates/ci-workflow.yml` (from this skill) to `.github/workflows/ci.yml`
- Run on push to main and PR to main
- **CI must mirror local commands** — same `npm run test:all` scripts and `--test-timeout` flags as local
- Set **`timeout-minutes: 20`** on the job so a stuck suite does not burn runner minutes indefinitely
- **Local-first workflow:** developers and agents run `npm run test:all` locally before push; CI confirms, it does not discover failures first
- **Verify CI actually runs** — workflows that exist but never trigger are a known failure mode

### Verification Gate
- [ ] `npm test` runs and returns 0
- [ ] `npm run test:integration` runs and returns 0
- [ ] `npm run build` succeeds
- [ ] Database has all tables from migrations
- [ ] Tester agent prompt is committed
- [ ] SCRUM_BOARD has **API and UI** test tasks for all planned phases
- [ ] SCRUM_BOARD has **frontend coding tasks** from UI manifest (one per PAGE)

---

## Phase N: Feature Implementation (Repeatable)

### Step N.1a — Backend Coding Tasks

#### Pre-dispatch Checklist
- [ ] Requirements clear (user story, acceptance criteria)
- [ ] Scope bounded (files, endpoints, expected behavior)
- [ ] Dependencies satisfied (previous phases complete)
- [ ] API test plans prepared (`tests/E0X-*-tests.md` — API sections)

#### Dispatch Rules
- **One worker at a time** — never dispatch multiple workers simultaneously to the same repo
- **Wait for completion** before dispatching next worker
- **Worker timeout:** 15 minutes max. If a task would take longer, split it
- **Worker scope:** backend coding only. Workers do NOT rewrite unrelated services
- **TDD is mandatory** for backend tasks — follow the **tdd-workflow** skill

#### Backend Worker Prompt Template
```
## TASK: [Task ID] — [Description]
Working directory: [path]
Track: BACKEND

### SCOPE
- Files to create/modify: [list]
- Endpoints to implement: [list]
- Dependencies: [list]

### ACCEPTANCE CRITERIA
(from user story + tests/E0X-* API sections)

### METHOD — TDD (mandatory)
1. Derive behaviors from ACs and tests/E0X-*.md API sections
2. RED → GREEN → REFACTOR in __tests__/
3. Name tests TEST-E0X-ROLE-NN-P1 / -N1

### RULES
- Commit locally but DO NOT push
- Do not implement frontend pages in this task
```

### Step N.1b — Frontend Coding Tasks (MANDATORY)

Required for every `PAGE_*` row in `plan/UI_IMPLEMENTATION_MANIFEST.md` for this epic. Dispatch after dependent APIs exist (or parallel only if APIs already done).

#### Frontend Pre-dispatch Checklist
- [ ] PAGE_ID + route from manifest
- [ ] Wireframe: `uidesign/PAGE_*.md` (all states)
- [ ] `uidesign/DESIGN_SYSTEM.md` + mockup if exists

#### Frontend Worker Prompt Template
```
## TASK: [C[N]-UI-XX] — Implement [PAGE_ID]
Track: FRONTEND

### DESIGN SOURCE (read all first)
- Wireframe: uidesign/PAGE_XXX.md
- Route: uidesign/INDEX.md
- Mockup: uidesign/assets/ (if any)

### SCOPE
- Pages, components, routes, role guards
- APIs to wire: [list from UI_API_BINDING_MANIFEST for this PAGE]

### UI DEFINITION OF DONE (three gates)
**N.3b Functional:** route + role guard + all UI states
**N.3c Fidelity:** matches wireframe + mockup (`uidesign/assets/`)
**N.3d API Binding:** every row in UI_API_BINDING_MANIFEST for this PAGE is ✅ or ⏸️ Deferred (hidden in UI)

### ANTI-PATTERNS (reject in review)
- Hardcoded KPI numbers (loyalty points, notification badges) without API
- Buttons visible in mockup but disabled with no Deferred waiver
- Static labels where API returns IDs (customer name, therapist name)
- `npm run test:ui` route-only checks as sole UI proof

### RULES
- No placeholder "coming soon" for contracted PAGE rows unless marked Deferred in manifest
- Update UI_IMPLEMENTATION_MANIFEST (Functional/Fidelity) and UI_API_BINDING_MANIFEST after each gate
- Commit locally but DO NOT push
```

#### Post-worker Verification (both tracks)
- [ ] `git diff --stat` within expected scope
- [ ] `npm run build` succeeds
- [ ] Backend: `npm test` for affected module
- [ ] Frontend: `npm run test:ui` for affected PAGE(s)

---

### Step N.2 — Unit Test Validation

Before integration testing, verify unit test coverage:

- [ ] Every new service module has `__tests__/` directory
- [ ] All unit tests pass: `npm test`
- [ ] Tests cover positive + negative scenarios
- [ ] Tests clean up after themselves

**If unit tests are missing or failing:** fix before proceeding to integration tests.

---

### Step N.3 — Integration Testing (MANDATORY GATE)

**Local-first rule:** integration tests must **pass locally** (`npm run test:integration` or `npm run test:all` on Docker) before any commit is pushed to GitHub. CI is a safety net — not the first place tests run. Hung tests without timeouts will stall CI and block the team.

#### Tester Agent Dispatch

```
## TASK: T[N] — Phase [N] Integration Tests

Read your role definition in agent-prompts/tester.md.

### TEST PLANS
[List test plan files for this phase]

### YOUR JOB
1. Study test plans — identify all scenarios
2. Create executable test file in tests/integration/
3. Run against local Docker backend using setup.ts helpers
4. Every test must have a timeout (default 30s; HTTP calls 10s via setup.ts)
5. File bugs for EVERY failure in bugs/BUG-XXX-title.md
6. Publish test report in plan/TEST_REPORT_E0X.md
7. Run npm run test:integration locally — all green before finishing
8. Commit locally but DO NOT push

### TARGET
All tests pass locally within reasonable time (< 10 min suite) before phase is marked Done.
```

#### Tester Report Required Fields
- Pass/fail count per test plan
- Detailed results table
- Bugs filed with severity
- Recommendations

#### Phase Gate (API track)
- [ ] Integration test report published
- [ ] All critical/high severity API tests pass
- [ ] **`npm run test:all` passes locally** on Docker stack (unit + API integration, with timeouts)
- [ ] All bugs filed with clear reproduction steps

### Step N.3b — UI Functional Gate (after N.1b)

- [ ] Route exists and matches `uidesign/INDEX.md`
- [ ] Role guard correct (customer / therapist / admin)
- [ ] States: loading, empty, error, success — not just happy path
- [ ] `npm run test:ui` smoke passes for epic PAGE(s)
- [ ] Manifest **Functional** column ✅ in `plan/UI_IMPLEMENTATION_MANIFEST.md`

### Step N.3c — UI Fidelity Gate (after N.3b)

**Do not mark Fidelity ✅ from route smoke alone.**

- [ ] Wireframe layout implemented (desktop; mobile if spec requires)
- [ ] Design system tokens; shared components per `COMPONENTS_SHARED.md`
- [ ] Screenshot compared to `uidesign/assets/mockup-*.png` where mockup exists
- [ ] No dev labels (`PAGE_xxx`) visible in production UI
- [ ] Manifest **Fidelity** column ✅

### Step N.3d — UI–API Binding Gate (after N.3c)

**This is why integration tests can pass while the UI feels disconnected.**

- [ ] `plan/UI_API_BINDING_MANIFEST.md` rows for this epic reviewed
- [ ] Every display value fetched from API (or widget hidden if ⏸️ Deferred)
- [ ] Every button/link either calls API + updates UI or routes to implemented PAGE
- [ ] `npm run test:ui-bindings` passes (static anti-pattern linter)
- [ ] Playwright: at least one **write action** per epic (create session, add to cart, etc.) verified in browser
- [ ] Manifest **API Binding** column ✅

#### UI Tester Dispatch (optional sub-agent)
```
## TASK: T[N]-UI — Phase [N] UI Gate
Read agent-prompts/tester.md (UI section).
For each PAGE in manifest for epic E0N:
1. Verify route + selectors (Playwright or browser automation)
2. File bugs for layout/flow deviations from uidesign/PAGE_*.md
3. Publish plan/TEST_REPORT_E0N-UI.md
```

#### Combined Phase Gate (both tracks)
- [ ] API gate (N.3) **and** UI gates (N.3b + N.3c + N.3d) passed
- [ ] No blockers (critical bugs preventing next phase)
- [ ] `plan/RISKS.md` reviewed — statuses updated
- [ ] **Coordinator pushes** only after API + UI green locally
- [ ] **Customer progress summary sent** — include **browser demo** of new PAGE(s), not API-only
- [ ] **At the midpoint phase:** working demo in browser → midpoint payment milestone

---

### Step N.4 — Bug Fix Cycle

```
For each bug:
1. Assess: does it block the current phase? If yes, fix now. If no, file and continue.
2. Spawn fix agent with bug details and specific file scope
3. Verify: re-run the failing integration test
4. Mark bug resolved
5. Commit locally (coordinator pushes at phase gate)

Repeat until all critical bugs resolved.
```

#### Bug Fix Agent Template
```
## TASK: Fix [Bug ID] — [Description]

Working directory: [path]

### BUG
[Link to bug file or paste details]

### FILES TO MODIFY
[Specific files]

### METHOD — Regression test first (per the tdd-workflow skill)
1. If no existing test reproduces this bug, write one that FAILS on the
   current code (red) — this proves the bug
2. Fix the bug — minimal change, within the file scope above
3. The regression test now passes (green)

### VERIFICATION
After fixing:
1. npm run build
2. Re-run the regression test and the originally failing test:
   [specific test command]
3. Both must pass; full unit suite for the module still passes
```

---

## Coordinator Responsibilities

### Per Sprint
1. **Set up SCRUM_BOARD** with **backend tasks, frontend tasks (from manifest), API test tasks, UI test tasks**
2. **Verify Phase 0** is complete before any coding (including manifest → board import)
3. **Dispatch workers one at a time**, waiting for each to finish
4. **Per epic:** backend coding → API integration tests → **frontend coding → UI gate** (order flexible only if APIs pre-exist)
5. **Do NOT mark a phase Done** until **`npm run test:all` AND UI gate** pass locally
6. **Handle bug fix cycle** before moving to next phase
7. **Push to GitHub** only after API + UI green locally — never push to "see if CI passes"

### When Workers Fail
1. First failure: check error, may be environment issue
2. Second failure: stop, analyze root cause, split task into smaller slices
3. If requirements unclear: escalate to project owner
4. If environment issue: fix, then re-dispatch

### When Tester Finds Bugs
1. File all bugs (even low priority)
2. Fix critical/high bugs immediately
3. Medium/low bugs can be deferred to next sprint
4. Re-run tests after fixes
5. Update test report with resolved status

---

## Bug vs Change Request Triage (Canonical Definition)

This taxonomy is referenced by project-uat and project-delivery — it applies from the first sprint through the warranty period. **The signed artifacts decide, not opinion:**

| Category | Test | Handling |
|----------|------|----------|
| **Defect** | Behavior deviates from a signed artifact (story AC, behavior test, design spec, architecture convention) | Fixed free — bug cycle now, warranty after launch |
| **Specification gap** | The artifacts are silent or ambiguous about the behavior | ≤ XS effort: fix as goodwill AND update the artifact (story/glossary/design) so it's classifiable next time. Larger: becomes a change request |
| **New feature** | System works as specified; customer wants something different or more | Change request — always priced, never absorbed |

**Change requests** go through the quoter's "Post-Signing Change Orders" process: log in `plan/CHANGE_REQUESTS.md` → mini-quote → written approval → a substantial feature becomes a **delta epic** run through the mini-pipeline (planner Stages 3–6 for that epic only → change order → new phase on this board → targeted UAT). Never re-run the whole pipeline; the epic is the unit of change.

When the customer calls something a bug and the artifacts say otherwise, show them the artifact — this is exactly why the planner gets stories and designs signed off.

---

## Templates

| File | Purpose |
|------|---------|
| `templates/tester-agent-prompt.md` | Tester sub-agent (API + UI tracks) |
| `templates/ci-workflow.yml` | GitHub Actions CI (timeouts + `test:all`) |
| `references/PROCESS_GAP_ANALYSIS.md` | Why UI gets omitted; copy to `plan/` on gap projects |
| `examples/ely2-retrospective.md` | API gate lessons |
| `examples/ely3-ui-gap-retrospective.md` | UI traceability gap lesson |

### `SCRUM_BOARD.md`

Lives at the project root. The coordinator owns it; update after every task state change.

```markdown
# SCRUM BOARD — [Project]
**Sprint:** N | **Phase:** [current phase] | **Updated:** YYYY-MM-DD

Status legend: ⬜ Pending · 🔵 In Progress · ✅ Done · ❌ Blocked

## Backend Queue
| ID | Task | Epic | Agent | Status | Notes |
|----|------|------|-------|--------|-------|
| C1-01 | Auth API | E01 | backend | ⬜ | |

## Frontend Queue (from UI_IMPLEMENTATION_MANIFEST)
| ID | PAGE_ID | Task | Epic | Status | Notes |
|----|---------|------|------|--------|-------|
| C1-UI-01 | PAGE_001 | Login page | E01 | ⬜ | uidesign/PAGE_LOGIN.md |

## Testing Queue
| ID | Task | Agent | Status |
|----|------|-------|--------|
| T1 | E01 API integration | tester-api | ⬜ |
| T1-UI | E01 UI gate | tester-ui | ⬜ |

## Bug Queue
| ID | Severity | Source Test | Status |
|----|----------|-------------|--------|
| BUG-001 | 🔴 Critical | TEST-E01-... | ⬜ |
```

### `tests/integration/setup.ts`

```typescript
import request from 'supertest';
import { createApp } from '../src/app';
import { query } from '../src/platform/db';

const app = createApp();

/** Default HTTP timeout for integration calls — fail fast, don't hang CI */
const HTTP_TIMEOUT_MS = 10_000;
export const api = request(app).timeout(HTTP_TIMEOUT_MS);

// Auth helpers
export async function loginAs(role: string): Promise<string> {
  // Register if needed, login, return token
}

export async function authApi(role: string) {
  const token = await loginAs(role);
  return {
    get: (url: string) => api.get(url).set('Authorization', `Bearer ${token}`),
    post: (url: string) => api.post(url).set('Authorization', `Bearer ${token}`),
    put: (url: string) => api.put(url).set('Authorization', `Bearer ${token}`),
    delete: (url: string) => api.delete(url).set('Authorization', `Bearer ${token}`),
  };
}

// Database cleanup (full cascade, reverse dependency order)
export async function cleanupDb(): Promise<void> {
  // DELETE FROM child_tables ...
  // DELETE FROM parent_tables ...
}
```

### `package.json` Test Scripts

```json
{
  "scripts": {
    "test": "node --test --test-concurrency=1 --test-timeout=15000 --require ts-node/register 'src/**/__tests__/*.test.ts'",
    "test:integration": "node --test --test-concurrency=1 --test-timeout=30000 --require ts-node/register 'tests/integration/**/*.test.ts'",
    "test:ui": "node web/scripts/verify-routes.mjs",
    "test:ui-bindings": "node web/scripts/verify-api-bindings.mjs",
    "test:frontend": "npm run test:ui && npm run test:ui-bindings",
    "test:all": "npm run test && npm run test:integration && npm run test:frontend"
  }
}
```

Add Playwright (or stack equivalent) in Phase 0 for UI smoke. Per-test override when needed: `it('slow upload', { timeout: 60_000 }, async () => { ... })`. Do not disable timeouts globally.

---

## Bug File Format

Severity scale (used everywhere — bugs, gates, reports): 🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low

```markdown
# [BUG-XXX] Title

| Field | Value |
|-------|-------|
| **Status** | `[ ] New` |
| **Severity** | 🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low |
| **Source Test** | `TEST-E0X-ROLE-NN-P1` |
| **Component** | `src/path/to/file.ts` |

## Steps to Reproduce
1. ...
2. ...

## Expected Behavior
(from test case)

## Actual Behavior
(what happened)

## Suggested Fix
(code or approach)
```

---

## Antipatterns (DO NOT DO)

| Antipattern | Why It's Bad |
|-------------|--------------|
| Skipping Phase 0 | Tests won't run, DB won't work, blockers at the worst time |
| Dispatching 3+ workers at once | Git lock contention, lost commits |
| Marking phase Done without tests | Bugs accumulate, fix cost grows |
| Workers rewriting unrelated services | Token burn, scope creep, regressions (one agent burned 7.7M tokens this way) |
| Tester on premium model | Slow and expensive — use a flash-class model |
| Hardcoding DB credentials | Breaks across environments |
| No cleanup in test files | Cross-contamination, false failures |
| Migration SQL without IF NOT EXISTS | Can't re-run, breaks CI |
| CI workflows created but never wired up | Tests only run manually; regressions slip through |
| Pushing before local integration pass | Run `npm run test:all` locally first — CI failures waste time and block merges |
| Integration tests without timeouts | Hung tests stall CI; use 30s per test, 10s per HTTP call, < 10 min suite budget |
| API-only phase Done | Frontend skipped — dual-track gates exist for a reason |
| Conflating API tests with UI integration | supertest does not touch React; require N.3d binding gate |
| Fidelity without binding | Mockup-matching UI with hardcoded KPIs is not shippable |
| Decorative buttons (Export, notifications) | Wire API or hide until Deferred waiver |
| Placeholder dashboard shell | Violates manifest; use role-specific PAGE routes |
| UAT as substitute for UI implementation | UAT verifies; implementation must build UI first |
| Starting without DESIGN_APPROVAL | Designs must be frozen or explicitly waived |

---

## Exit Gate → Hand Off to project-uat

When ALL phases pass **API integration AND all three UI gates**:

- [ ] All API test reports published (`plan/TEST_REPORT_E0X.md`), all passing
- [ ] **`plan/UI_IMPLEMENTATION_MANIFEST.md` — Functional + Fidelity ✅ for all P1 rows**
- [ ] **`plan/UI_API_BINDING_MANIFEST.md` — API Binding ✅** (or ⏸️ Deferred with customer waiver)
- [ ] UI test reports published (`plan/TEST_REPORT_E0X-UI.md`) or UI gate checklists signed
- [ ] All critical/high bugs resolved and verified
- [ ] Build succeeds; **every contracted PAGE route works in browser** on Docker stack
- [ ] `npm run test:all` passes locally (unit + API integration + `test:frontend`)
- [ ] All commits pushed

### Security Gate

Verify against the security requirements in `plan/ARCHITECTURE.md`:

- [ ] Dependency audit clean of critical/high (`npm audit --omit=dev` or stack equivalent)
- [ ] Secrets scan — no credentials/keys/tokens in the repo
- [ ] **Authorization matrix tests pass**: every endpoint × every role, including cross-tenant access ("role A cannot read role B's data") — these belong in the integration suite
- [ ] Input validation on all write endpoints; standard error envelope leaks no stack traces
- [ ] Rate limiting on auth endpoints
- [ ] **No unapproved third-party hooks**: every external script, CDN resource, analytics/tracking snippet, or outbound link in the frontend traces to `plan/ARCHITECTURE.md` or the design spec. AI-generated and template code loves to smuggle in tracking snippets, attribution comments, and promotional links — grep for external URLs and audit each one

(The deeper vulnerability scan and production-config checks run later in project-delivery — this gate catches what's cheap to fix now.)

### NFR Gate

Verify the non-functional targets from `plan/ARCHITECTURE.md`:

- [ ] Load smoke test on key endpoints (a simple script with N concurrent requests is enough) — response times within the stated budget
- [ ] No N+1 query patterns on list endpoints; pagination works at realistic data volume

Then invoke the **project-uat** skill for user acceptance testing. Do not duplicate its process here — it owns UAT case generation, validation, execution, and sign-off.
