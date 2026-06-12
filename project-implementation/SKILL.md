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
- [ ] UI design documents (`uidesign/*.md`) and customer design approval (`plan/DESIGN_APPROVAL.md`)
- [ ] Architecture decisions (`plan/ARCHITECTURE.md` — tech stack, schema, API conventions, security requirements, NFR targets)

If any input is missing, run the upstream skill first.

**Risk tracking:** copy the risk items from the quoter's module plans into `plan/RISKS.md` (risk, probability, impact, mitigation, status). Review it at every phase gate — risks that were priced must be actively managed, not forgotten.

**Stack note:** templates in this skill assume Node.js/TypeScript (`npm`, `node:test`, `supertest`). Adapt commands and libraries to whatever `plan/ARCHITECTURE.md` specifies.

## Core Principles

1. **Test infrastructure is Phase 0** — before any feature code
2. **Every module ships with unit tests** — no exceptions
3. **Integration testing gates every phase** — tester agent runs after coding
4. **Bugs are found and fixed in the same sprint**
5. **One-at-a-time worker dispatch** — avoid lock contention
6. **Only the coordinator pushes** — workers and testers commit locally; the coordinator pushes after a phase gate passes

These rules encode a real retrospective — see `examples/ely2-retrospective.md` for the full story behind each one.

---

## Phase Structure

```
Phase 0: Test Infrastructure
Phase N: Feature Phases (repeatable)
  ├── N.1 Coding tasks (backend + frontend)
  ├── N.2 Unit test validation
  ├── N.3 Integration testing
  └── N.4 Bug fix cycle
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
- Create `npm run test:integration` script
- Create `tests/integration/setup.ts` with:
  - Supertest client pointed at the app
  - Auth helpers (`loginAs(role)`, `authApi(role)`)
  - Database reset helper (full cascade cleanup)
- Verify the app starts and health endpoint responds

### 0.5 Test Plan Inventory
- Catalog all test plan files (`tests/E0X-*.md`)
- Map each to the phase that owns it
- Add test tasks (T1A, T1B, etc.) to `SCRUM_BOARD.md` (see template below)

### 0.6 Tester Agent Prompt
- Copy `templates/tester-agent-prompt.md` (from this skill) to the project's `agent-prompts/tester.md`
- Replace `MODEL_NAME` with a fast/cheap model (e.g. a flash-class model) — premium models are wasted on test execution
- It includes: test methodology, bug format, report format, quality gates

### 0.7 CI/CD Pipeline (if applicable)
- Create GitHub Actions CI workflow (lint, typecheck, test, build)
- Run on push to main and PR to main
- **Verify CI actually runs** — workflows that exist but never trigger are a known failure mode

### Verification Gate
- [ ] `npm test` runs and returns 0
- [ ] `npm run test:integration` runs and returns 0
- [ ] `npm run build` succeeds
- [ ] Database has all tables from migrations
- [ ] Tester agent prompt is committed
- [ ] SCRUM_BOARD has test tasks for all planned phases

---

## Phase N: Feature Implementation (Repeatable)

### Step N.1 — Coding Tasks

#### Pre-dispatch Checklist
- [ ] Requirements clear (user story, acceptance criteria)
- [ ] Scope bounded (files, endpoints, expected behavior)
- [ ] Dependencies satisfied (previous phases complete)
- [ ] Test plans prepared for this phase

#### Dispatch Rules
- **One worker at a time** — never dispatch multiple workers simultaneously to the same repo
- **Wait for completion** before dispatching next worker
- **Worker timeout:** 15 minutes max. If a task would take longer, split it
- **Worker scope:** coding only. Workers do NOT rewrite unrelated services
- **TDD is the default method for coding tasks**: workers follow the **tdd-workflow** skill (behavior list from ACs → red → green → refactor). Exempt: pure UI layout/styling tasks. Because sub-agents don't inherit your context, the TDD instruction MUST be embedded in the dispatch prompt — the template below includes it; don't strip it

#### Worker Prompt Template
```
## TASK: [Task ID] — [Description]
Working directory: [path]

### SCOPE
- Files to create/modify: [list]
- Endpoints to implement: [list]
- Dependencies: [list]

### ACCEPTANCE CRITERIA
(from user story)

### METHOD — TDD (mandatory for logic/API tasks)
Follow the tdd-workflow skill. If it is not loadable in your context, apply this loop:
1. Derive a behavior list from the acceptance criteria and the planned
   behavior tests (tests/E0X-*.md) — positive, negative, boundary, error
2. RED: write failing tests in `__tests__/[module].test.ts` + a stub;
   verify they fail for the right reason
3. GREEN: implement the minimum to pass (error cases → boundary → positive)
4. REFACTOR: clean up with tests staying green; no new behavior

- Name tests after the planned IDs (TEST-E0X-ROLE-NN-P1 / -N1) for traceability
- Cover every scenario from the test plan; add edge cases the plan missed

### RULES
- Commit locally but DO NOT push
- If you encounter a bug in another module, note it but don't fix it
- Focus only on the specified scope
```

#### Post-worker Verification
- [ ] Check `git diff --stat` — changes within expected scope
- [ ] Run `npm run build` — must succeed
- [ ] Run `npm test` for the affected module — must pass
- [ ] Review commit quality (message, atomicity)

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

#### Tester Agent Dispatch

```
## TASK: T[N] — Phase [N] Integration Tests

Read your role definition in agent-prompts/tester.md.

### TEST PLANS
[List test plan files for this phase]

### YOUR JOB
1. Study test plans — identify all scenarios
2. Create executable test file in tests/integration/
3. Run against backend using setup.ts helpers
4. File bugs for EVERY failure in bugs/BUG-XXX-title.md
5. Publish test report in plan/TEST_REPORT_E0X.md
6. Commit locally but DO NOT push

### TARGET
All tests pass before phase is marked Done.
```

#### Tester Report Required Fields
- Pass/fail count per test plan
- Detailed results table
- Bugs filed with severity
- Recommendations

#### Phase Gate
- [ ] Integration test report published
- [ ] All critical/high severity tests pass
- [ ] All bugs filed with clear reproduction steps
- [ ] No blockers (critical bugs preventing next phase)
- [ ] `plan/RISKS.md` reviewed — statuses updated, new risks added
- [ ] Coordinator pushes the phase's commits
- [ ] **Customer progress summary sent** — 5–10 lines: what was completed, what's next, any decisions needed
- [ ] **At the midpoint phase:** give the customer a working demo → this triggers the midpoint payment milestone from the proposal

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
1. **Set up SCRUM_BOARD** with coding tasks AND test tasks
2. **Verify Phase 0** is complete before any coding
3. **Dispatch workers one at a time**, waiting for each to finish
4. **After each phase's coding is done** → spawn tester agent
5. **Do NOT mark a phase Done** until integration tests pass
6. **Handle bug fix cycle** before moving to next phase
7. **Push to remote** only after the phase gate passes

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

### `SCRUM_BOARD.md`

Lives at the project root. The coordinator owns it; update after every task state change.

```markdown
# SCRUM BOARD — [Project]
**Sprint:** N | **Phase:** [current phase] | **Updated:** YYYY-MM-DD

Status legend: ⬜ Pending · 🔵 In Progress · ✅ Done · ❌ Blocked

## Coding Queue
| ID | Task | Epic | Agent | Status | Notes |
|----|------|------|-------|--------|-------|
| C1A-01 | Auth endpoints | E01 | backend-coder | ⬜ | |

## Testing Queue
| ID | Task | Agent | Status |
|----|------|-------|--------|
| T1A | Phase 1A Integration Tests | tester | ⬜ |

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
export const api = request(app);

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
    "test": "node --test --test-concurrency=1 --require ts-node/register 'src/**/__tests__/*.test.ts'",
    "test:integration": "node --test --require ts-node/register 'tests/integration/**/*.test.ts'",
    "test:all": "npm run test && npm run test:integration"
  }
}
```

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

---

## Exit Gate → Hand Off to project-uat

When ALL phases pass their integration test gates:

- [ ] All test reports published (`plan/TEST_REPORT_E0X.md`), all passing
- [ ] All critical/high bugs resolved and verified
- [ ] Build succeeds; application runs (backend + frontend)
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
