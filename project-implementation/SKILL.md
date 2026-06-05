# Implementation & Testing Workflow

A battle-tested workflow for taking a project from planning (user stories, test plans, UI designs) through implementation with integrated testing at every phase.

## When to Use

After the planning phase is complete and you have:
- User stories with acceptance criteria
- Behavior test plans (per-epic, per-role)
- UI design documents
- Architecture decisions (tech stack, schema, API design)

## Core Principles

1. **Test infrastructure is Phase 0** — before any feature code
2. **Every module ships with unit tests** — no exceptions
3. **Integration testing gates every phase** — tester agent runs after coding
4. **Bugs are found and fixed in the same sprint**
5. **One-at-a-time worker dispatch** — avoid lock contention

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

### 0.1 Database Setup
- Verify database is running and accessible
- Set up `.env` with correct connection string and port
- Run all migrations — verify they complete cleanly
- Fix any migration issues (missing `IF NOT EXISTS`, wrong paths, ownership)

### 0.2 Unit Test Runner
- Install test dependencies (`jest`, `vitest`, or `node:test`)
- Create `npm test` script that runs all `__tests__/` directories
- Verify a smoke test passes (e.g., `1+1=2`)
- Add `--test-concurrency=1` if tests share database state

### 0.3 Integration Test Runner
- Install supertest (or equivalent HTTP test library)
- Create `npm run test:integration` script
- Create `tests/integration/setup.ts` with:
  - Supertest client pointed at the app
  - Auth helpers (`loginAs(role)`, `authApi(role)`)
  - Database reset helper (full cascade cleanup)
- Verify the app starts and health endpoint responds

### 0.4 Test Plan Inventory
- Catalog all test plan files (`tests/E0X-*.md`)
- Map each to the phase that owns it
- Add test tasks (T1A, T1B, etc.) to SCRUM_BOARD.md

### 0.5 Tester Agent Prompt
- Create `agent-prompts/tester.md` following the tester agent template
- Configure for `deepseek-v4-flash` model (fast, cheap)
- Include: test methodology, bug format, report format, quality gates

### 0.6 CI/CD Pipeline (if applicable)
- Create GitHub Actions CI workflow (lint, typecheck, test, build)
- Run on push to main and PR to main

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

### UNIT TESTS REQUIRED
- Create `__tests__/[module].test.ts` covering:
  - All positive scenarios
  - All negative/error scenarios from test plan
  - Edge cases

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

---

### Step N.4 — Bug Fix Cycle

```
For each bug:
1. Assess: does it block the current phase? If yes, fix now. If no, file and continue.
2. Spawn fix agent with bug details and specific file scope
3. Verify: re-run the failing integration test
4. Mark bug resolved
5. Commit and push

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

### VERIFICATION
After fixing:
1. npm run build
2. Re-run the failing test:
   [specific test command]
3. Test must pass
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

## Templates

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

### SCRUM_BOARD Test Tasks

```markdown
## Testing Queue

| ID | Task | Agent | Status |
|----|------|-------|--------|
| T1A | Phase 1A Integration Tests | tester | ⬜ Pending |
| T1B | Phase 1B Integration Tests | tester | ⬜ Pending |
```

---

## Bug File Format

```markdown
# [BUG-XXX] Title

| Field | Value |
|-------|-------|
| **Status** | `[ ] New` |
| **Severity** | 🔴 Critical / 🟡 Medium / 🟢 Low |
| **Source Test** | `TEST-EX-ROLE-NN` |
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
| Workers rewriting unrelated services | Token burn, scope creep, regressions |
| Tester on premium model | Slow and expensive — use flash |
| Hardcoding DB credentials | Breaks across environments |
| No cleanup in test files | Cross-contamination, false failures |
| Migration SQL without IF NOT EXISTS | Can't re-run, breaks CI |
