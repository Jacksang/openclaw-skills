# ELY2 Retrospective Report

**Date:** 2026-06-05  
**Project:** ELY2 Wellness Platform  
**Team:** Eva2 (Coordinator) + Sub-agents (Backend Coder, Frontend Coder, Tester, Infra Coder, CI Coder)

---

## Project Stats

| Metric | Count |
|--------|-------|
| Commits | 120 |
| Backend source files | 97 |
| Migration files | 13 |
| User story test plans | 17 |
| Integration test files | 4 |
| Bugs filed | 12 |
| Unit tests | 76 |
| Integration tests | 236 |
| Backend tasks | 57 |
| Frontend tasks | 17 |
| Infrastructure tasks | 9 |

---

## ✅ What We Did Right

### 1. Planning-First Approach
User stories, behavior tests, and UI designs were all prepared **before** coding started. This gave workers clear acceptance criteria and test plans to reference. The 17 test plan files (E01-E07 across all personas) provided a comprehensive specification that both coders and testers could work from.

### 2. Phased Delivery
Breaking work into 4 distinct phases (Foundation → Core → Commerce → Infrastructure) kept things manageable. Each phase had clear boundaries, dependencies, and verification gates.

### 3. Agent-Based Worker Dispatch
Using isolated sub-agents for coding tasks worked well. Each worker received a focused, scoped task and could work independently. This parallelized development effectively once the lock contention issue was resolved.

### 4. Database Design
37 tables across 7 schemas (auth, care, commerce, growth, media, platform, reports) with proper foreign keys, check constraints, and unique indexes. The schema design held up well — no major structural changes were needed during implementation.

### 5. Integration Tests Found Real Bugs
The integration test suite caught 12 bugs that unit tests missed entirely:
- BUG-001: SET LOCAL parameterized queries (blocked ALL authenticated routes)
- BUG-002: Missing INSERT RLS policies (blocked registration)
- BUG-003: Missing migration tables (blocked password reset + OTP)
- BUG-PROFILE-500: Missing column reference (crashed profile endpoint)
- BUG-E04-EXPORT-SCHEMA: Missing column reference (crashed export)
- BUG-E02-STATE-MACHINE-ERROR: Wrong HTTP error codes
- BUG-AUTH-ROLE-NO-ASSIGN: Registration ignored role field
- BUG-E05-001: Fraud route not mounted (404)
- BUG-E05-002: Checkout never decremented stock
- BUG-E05-003: Catalog showed inactive/deleted products
- BUG-E05-004: Cursor pagination crashed on type inference
- BUG-E07-001: Inconsistent error response shape

### 6. Bug Fix Velocity
All 12 bugs were identified, fixed, and verified within the same session. The fix-and-retest cycle was fast: spawn fix agent → verify → commit → push.

### 7. Migrations Were Self-Correcting
Migration issues (missing IF NOT EXISTS, wrong paths, ownership problems) were caught early and fixed. The migration runner's tracking table ensured we never double-applied.

---

## ❌ What Needs Improvement

### 1. 💀 CRITICAL: Integration Testing Was Skipped Initially
**Root cause:** The tester agent prompt existed (`agent-prompts/tester.md`) but was never wired into the dispatch workflow. The SCRUM_BOARD had a Tests column that stayed at `0/0` through all 4 phases.

**What happened:** After each phase's coding completed, the coordinator moved directly to the next phase's coding tasks instead of spawning a tester agent. This violated the established "Frontend Done = UI matches design + APIs fully integrated" rule.

**Fix applied:** Added "Mandatory Testing Phase Rule" to MEMORY.md and Coordinator Duties. Added T1A-T1D testing tasks to SCRUM_BOARD with explicit workflow steps.

### 2. 🔧 Unit Tests Were Sparse
Only 5 of 14 service modules had unit tests (commerce + referrals). Nine modules had zero unit tests: auth, sessions, imaging, reports, notifications, platform, and users. Unit tests should exist for ALL critical modules, not just commerce.

### 3. 🔄 Concurrent Workers Caused Git Lock Contention
When 3 workers were dispatched simultaneously, all 3 hit `index.lock` conflicts because the gateway held the lock. Two of the three workers failed entirely (0 commits). Fix: stagger dispatch, wait for each to complete before starting next.

### 4. 📦 Test Infrastructure Didn't Exist at Start
When testing was finally attempted:
- No `supertest` installed
- No `test:integration` npm script
- No shared test helper (setup.ts)
- No `tests/integration/` directory
- Database not migrated (port mismatch, ownership issues)

All of this should have been set up before Phase 1A coding even started.

### 5. 🐛 Migration Files Had Quality Issues
- `migrate` script path was wrong in package.json
- Several `CREATE TABLE` statements lacked `IF NOT EXISTS`
- Migration 005 and 008 had embedded `INSERT INTO schema_migrations` with wrong column names
- Schema ownership was mixed (dell vs ely2 user)

### 6. 🔥 Agent Token Burn
One test-fix agent burned 7.7M tokens in 1 hour and timed out without completing. The root cause: the agent tried to fix service code (referral-list.ts) instead of just tests, then went into loops fixing the same issues repeatedly.

### 7. 🧪 Parallel Test Execution Caused False Failures
When unit tests ran in parallel, cross-contamination caused failures even though each file passed individually. Fixed with `--test-concurrency=1`, but the real fix should be better test isolation.

### 8. ⌛ Agent Timeouts
Several sub-agents timed out mid-work (1h limit). Large tasks need to be broken into smaller slices. Agents that go beyond "just fix tests" into service rewrites burn tokens exponentially.

### 9. 📝 No CI Running
GitHub Actions workflows were created (`.github/workflows/ci.yml`, `.github/workflows/cd.yml`) but never connected to the repository. Tests only run manually. CI should gate every push.

### 10. 🔌 Tester Agent Not on Flash Model
The tester prompt specifies `deepseek-v4-flash` for fast execution, but all test agents ran on `deepseek-v4-pro`. Flash model would reduce cost and speed up feedback loops.

---

## 📊 Process Metrics

| Phase | Coding Tasks | Bugs Found | Bugs After Fix | Test Cases |
|-------|-------------|------------|----------------|------------|
| Planning | — | — | — | 141 scenarios |
| 1A Foundation | 31 | 3 | 0 | 38 |
| 1B Core | 23 | 4 | 0 | 71 |
| 1C Commerce | 11 | 4 | 0 | 78 |
| 1D Infrastructure | 9 | 1 | 0 | 49 |
| **Total** | **74** | **12** | **0** | **236** |

---

## 🔮 Recommendations

1. **Test infrastructure should be Phase 0** — set up test runner, helpers, and CI before any coding
2. **Unit tests should be mandatory per module** — no module merged without `__tests__/`
3. **CI gate on every push** — lint, type-check, unit tests, integration tests
4. **Tester agent on flash model** — faster, cheaper, good enough for test execution
5. **Worker task size limits** — if a task takes >15 min for an agent, split it
6. **One-at-a-time worker dispatch** — avoid git lock contention
7. **Migration linting** — validate `CREATE TABLE` has `IF NOT EXISTS`, check paths, check ownership
8. **Test isolation** — each test file should clean up after itself, or run sequentially
9. **Bug tracker integration** — tie bugs to specific commits, auto-close on fix verification
10. **Daily integration test runs** — catch regressions early, not at end of sprint
