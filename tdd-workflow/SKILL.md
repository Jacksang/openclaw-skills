---
name: "tdd-workflow"
description: "Test-driven development: generate tests first, stub, then implement to pass all tests."
---

# TDD Workflow

Use when the user asks to write a method/function with tests (TDD approach). Triggers on keywords: "tdd", "test-driven", "write a method with tests", or similar.

## Workflow

### Phase 1: Parse Request

1. Extract from user description:
   - **Method name** (e.g., `calculateFibonacci`)
   - **Parameters & types** (e.g., `n: number`)
   - **Return type** (e.g., `number | null`)
   - **Constraints** (e.g., `n in [0, 1000]`, edge cases mentioned)
   - **Output file path** (default: `src/methodName.js` + `src/methodName.test.js`)

2. If information is missing, infer reasonable defaults and note assumptions.

### Phase 2: Generate Test File

Create the test file FIRST. Cover ALL these categories:

- **Positive cases** — valid inputs with expected correct outputs
- **Negative/invalid inputs** — out-of-range, wrong types, null/undefined
- **Boundary cases** — min value, max value, 0, 1, overflow
- **Error cases** — throws on bad input, handles edge conditions gracefully
- **Performance hints** — if constraints imply complexity concerns (e.g., n=1000)

Use Node.js built-in `assert` module or a lightweight test runner. Keep tests clear and independent.

### Phase 3: Generate Stub Implementation

Create the implementation file with:
- Correct function signature
- A stub body that returns `null` / throws / returns placeholder
- JSDoc comment with the method description

This ensures tests **fail** initially (TDD red phase).

### Phase 4: Verify Tests Fail

Run the test file. Confirm at least some tests fail. If all pass immediately, the stub is wrong — fix the stub to be a proper failing implementation.

### Phase 5: Implement (Green Phase)

Fix each failing test iteratively:
1. Read the failing test to understand expected behavior
2. Update the implementation to make it pass
3. Re-run tests
4. Repeat until ALL tests pass

Prioritize: negative/error cases → boundary cases → positive cases.

### Phase 6: Final Verification

- All tests pass ✅
- Implementation is complete (not stub)
- No commented-out tests
- Code is clean and readable

## Output

- `src/<methodName>.js` — final implementation
- `src/<methodName>.test.js` — comprehensive test suite
- Brief summary of what was implemented and tested

## Notes

- Keep the implementation focused — only satisfy the tests
- If a test case seems unreasonable, note it but still pass it
- For complex methods, consider breaking into smaller functions
- Always use the project's existing test framework if one is already set up
