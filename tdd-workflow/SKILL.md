---
name: tdd-workflow
description: "Atomic red-green-refactor TDD loop for a single unit of work: a pure function, a class public method, an API endpoint, or one small requirement/acceptance criterion. Use when the user mentions TDD, test-driven, test-first, red-green, asks to write code with tests, fix a bug (regression test first), or when implementing a scoped coding task from a requirement."
---

# TDD Workflow

The atomic inner loop for one unit of work: derive behaviors from the requirement → write failing tests → implement minimally → refactor under green. Requirement first, always — the code is the *output* of the tests, not the other way around.

## When to Use / When Not

**Use TDD when expected behavior is specifiable before the code exists:**

| Situation | Note |
|-----------|------|
| New pure logic (parser, calculator, validator, state machine) | Behaviors fully enumerable up front |
| New API endpoint | The contract (status codes, envelope, authz) is the test list |
| Public method on a class | Forces design through the public interface |
| Bug fix | Regression test FIRST — proves the bug exists, then proves the fix |
| Refactoring untested code | Pin current behavior with tests, then refactor safely |
| One small requirement / acceptance criterion | Decompose into the units above; every AC gets ≥ 1 test |

**Skip TDD for:** UI layout/styling, exploratory spikes (write tests when the code graduates from throwaway), one-off scripts, pure glue/config with no logic. Brittle tests on volatile surfaces punish change.

## Requirement Sources

**Inside the project pipeline** (invoked from a project-implementation worker task): the requirement is already written — use the user story's acceptance criteria, the matching behavior tests (`tests/E0X-[role]-tests.md`, IDs like `TEST-E0X-ROLE-NN-P1`), and the API conventions in `plan/ARCHITECTURE.md`. Name executable tests after the planned test IDs for traceability.

**Standalone** (user asks directly): extract from the request — unit name, inputs/outputs, constraints, edge cases. If ambiguous, infer reasonable defaults and **state your assumptions before writing tests** so the user can correct them cheaply.

---

## The Loop

```
0. LIST    — derive behavior list from the requirement
1. RED     — write tests + stub; verify tests FAIL for the right reason
2. GREEN   — implement the minimum to pass; iterate per behavior
3. REFACTOR— clean up with tests staying green
4. DONE    — completion checklist
```

### Step 0: Behavior List

Before any code, enumerate the behaviors in plain language — one line each:

```
calculateDiscount(order, customer)
- returns 0 for orders below threshold            (positive)
- applies 10% for gold customers                  (positive)
- caps discount at 50                             (boundary)
- throws TypeError on null order                  (error)
- rejects negative order totals                   (negative)
```

Cover: **positive** (happy paths), **negative** (invalid input), **boundary** (min/max/0/1/empty/overflow), **error** (throws/rejects correctly). For async code, include rejection paths, not just resolutions.

### Step 1: Red

Write the test file from the behavior list, then a stub:

- Stub has the correct signature and JSDoc, but returns `null` / throws `new Error('not implemented')`
- Run the tests. **Every test must fail, and fail for the right reason** (assertion failure or not-implemented — not a syntax error or bad import)
- If any test passes against the stub, the test is broken — fix it now, while it's cheap

### Step 2: Green

Implement the minimum code to pass, working through behaviors in this order: **error/negative cases → boundary cases → positive cases** (error handling shapes the structure; bolting it on last causes rewrites). Re-run after each behavior. Do not add behavior no test demands.

### Step 3: Refactor (Do Not Skip)

With all tests green: remove duplication, improve names, extract helpers, simplify conditionals. Re-run tests after each change. **No new behavior in this step** — if you discover a missing behavior, add it to the list and run a fresh red-green cycle for it.

### Step 4: Done Checklist

- [ ] Every behavior in the list has a test; all tests pass
- [ ] Tests fail meaningfully if the implementation is broken (spot-check by mentally reverting a line)
- [ ] No commented-out tests, no `.skip`, no stub remnants
- [ ] Public interface documented (JSDoc); implementation only satisfies the tests — nothing speculative

---

## Per-Unit-Type Guidance

### Pure Function / Module

Unit test, co-located: `src/foo.js` + `src/foo.test.js`. No mocks needed — if you need mocks for a "pure" function, it isn't pure; split it.

### Class Public Method

- Test **through the public interface only** — never test private methods directly; they're covered via the public behaviors that use them
- Each test constructs its own instance (no shared mutable state between tests)
- Mock only external boundaries the class talks to (DB client, HTTP, clock); never mock the class under test

### API Endpoint

Integration-style test in `tests/integration/` (matching project-implementation's layout), using the shared `setup.ts` helpers when in the pipeline. **Timeouts:** 10s per HTTP call (via `setup.ts`), 30s per test (`--test-timeout=30000`) — run locally and pass before push.

The behavior list is the contract:

- Correct status codes per scenario (200/201, 400 validation, 401 unauthenticated, 403 wrong role, 404, 409 conflicts)
- Response envelope shape and exact field names (per `ARCHITECTURE.md` when available)
- Authorization: one test per relevant role, including "role A cannot access role B's data"
- Validation: each required field missing/invalid → 400 with the standard error shape

### One Small Requirement (AC)

Decompose: which endpoints + which functions implement it? Run this loop per unit, bottom-up (pure logic first, endpoint last). Map each AC to at least one test and say which.

---

## Conventions

- **ES modules** (`type: module`), `async`/`await` — no callbacks, no CommonJS
- Default runner: **`node:test`** + **`node:assert/strict`** (zero dependencies); if the project already has a test framework, use that instead — never introduce a second one
- Unit tests co-located (`src/foo.test.js`); endpoint tests in `tests/integration/`; shared fixtures/helpers in `tests/`
- Each test independent: own setup, own teardown, no ordering assumptions

**Assertion gotchas (real failures, not theory):**
- `assert.throws` regex matching is **case-sensitive** — match the actual error message casing
- `strictEqual` does **not** coerce types: `strictEqual(0n, 0)` fails. Match BigInt with BigInt literals, Number with Number
- For async: `await assert.rejects(fn(), /message/)` — forgetting `await` makes the test pass vacuously

---

## Position in the Skill Chain

This skill is the **micro loop** nested inside **project-implementation**'s macro process (phases, worker dispatch, gates). A worker agent receiving a coding task applies this loop to satisfy its "unit tests required" obligation; the planned behavior tests and ACs are its Step 0 input. Standalone use (no pipeline) works identically — the requirement just comes from the user instead of the plan.

Do not duplicate this loop's content into other skills; reference it by name.

---

## Antipatterns

| Antipattern | Fix |
|-------------|-----|
| Implementation first, tests back-filled | Tests first — back-filled tests inherit the code's blind spots |
| Tests pass against the stub | Broken test; fix before implementing |
| Testing private methods | Test through the public interface only |
| Mocking everything | Mock external boundaries only; over-mocked tests verify the mocks |
| Skipping refactor once green | Step 3 is part of the loop, not optional polish |
| One giant unit | If the behavior list exceeds ~10 items, split the unit |
| Speculative implementation | Only write code a test demands |
| Bug fixed without a regression test | Red first: the test must fail on the unfixed code |
