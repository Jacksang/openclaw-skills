# Bug Fixer Skill

## Overview

This skill provides a rigorous, multi-step workflow for fixing bugs submitted via the Bug Tracker (http://localhost:3003). It eliminates the ping-pong cycle of "I said it was fixed but it wasn't" by enforcing:

1. **Test-case-first**: Every bug is reviewed by a test agent before any code is written.
2. **Build verification**: Every fix MUST pass a full build before it can be committed.
3. **Verification loop**: Fixes are automatically UAT-tested with browser screenshots. ALL bugs require browser testing — even "backend-only" changes can crash SSR.
4. **Retry on failure**: Failed UAT spawns a new fix cycle; never report "done" until tests pass.
5. **Formal sign-off**: Final report with explicit PASS/FAIL and a reminder for human UAT.

## Workflow

### Step 0: Prerequisites Check

- Bug Tracker service running on `http://localhost:3003` (project at `bug-tracker/`)
- Target project directory exists at `~/.openclaw/workspace/projects/<project>` or `~/projects/<project>`
- Project has `bugs/` folder with bug files in format `BUG-###-title.md`
- Bug files use the template (Status: `[ ] New` or `[~] Reopened` → needs fixing)
- Ensure project can build: `npm install` if new deps were added

### Step 1: Scan for Pending Bugs

When user says bug fixing is needed (e.g. "fix bugs", "test is done", "I reported bugs", or targeting a specific project):

1. Identify the project:
   - If user specifies a project name, use that.
   - If not specified, ask: "Which project's bugs should I fix?"
2. Read all bug files in `bugs/` folder.
3. Filter for bugs with Status: `[ ] New` **or** `[~] Reopened`.
4. If no pending bugs found → report & stop.
5. Sort bugs numerically (BUG-001, BUG-002...).
6. Report to user: "Found [N] bugs to fix: BUG-XXX, BUG-YYY..."

### Step 2: Per-Bug Test-Case Generation

For EACH pending bug (process sequentially, one at a time):

1. Spawn a **test sub-agent** (isolated, `mode="run"`).
2. Task for the test agent:
   - Read the bug file (steps to reproduce, expected behavior, actual behavior).
   - Based on the bug description, write **test cases** covering:
     - Reproduction steps (how to verify the bug exists).
     - Expected behavior after fix (how to verify it's fixed).
     - Edge cases around the fix.
   - Write test cases to a file at: `bugs/test-cases/BUG-XXX-test-cases.md`
   - Return structured PASS/FAIL: did it produce test cases?

3. Wait for test sub-agent completion.
4. On failure → report to user, skip this bug, continue to next.
5. Verify the test cases file was actually created.

### Step 3: Determine Fix Scope

Based on the bug description (frontend, backend, or both), decide what fix slices are needed. However, **scope does NOT affect UAT requirements** — ALL bugs require build verification + browser UAT (see below).

### Step 4: Spawn Fix Sub-agents

For each scope (frontend, backend, or both):

1. **Create a fix sub-checklist** first (the main session owns this):
   - For backend bugs: break into function-level slices: schema changes, service changes, API endpoint changes, test changes.
   - For frontend bugs: break into component-level slices: UI changes, state/logic changes, integration changes.
   - Keep each slice narrow (one file or one function per slice).

2. **Spawn fix sub-agents** (`mode="run"`, isolated, 5-10 min timeout):
   - One sub-agent per slice.
   - Each agent gets: the bug file, the specific slice instructions, allowed files.
   - Agents make local code changes (no commit yet).

3. **Monitor actively:**
   - After spawning, set up a monitoring loop (use session_yield, check subagents periodically).
   - The main session must NOT yield blindly — use the **subagent-watchdog cron** (runs every 10 min) to detect stalls.
   - If a sub-agent is running >10 min without progress → kill it, re-evaluate slice size, restart.
   - On sub-agent failure → increment retry count. Max 3 retries per slice, then escalate.

4. **Verify fix artifacts:**
   - After each sub-agent completes, verify the changes were actually made (read the changed files).
   - Check that the fix addresses the root cause, not just symptoms.
   - If fix is incomplete → spawn a new slice agent, do NOT reuse the existing one.

### Step 5: Build Verification (MANDATORY — ALL Bug Types)

**NEVER SKIP THIS STEP.** Even a one-line change can crash the entire application.

1. **Run the build:**
   ```
   cd ~/projects/<project>
   npm run build
   ```
   (or equivalent: `vite build`, `svelte-kit build`, `next build`, `tsc`, `make`, etc.)

2. **If build fails:**
   - ❌ **Class 1 CRASH BUG — DO NOT COMMIT**
   - This means the fix broke the application. It is NOT fixed.
   - Do NOT update the bug file status.
   - Do NOT report to the user as "done."
   - Immediately go back to Step 4 (spawn new fix sub-agents to fix the build).
   - Report to user: "Build failed due to our fix — fixing the crash now."
   - After repairing, rebuild and verify again.

3. **If build passes:**
   - ✅ Proceed to Step 6 (UAT test).

### Step 6: UAT Test with Browser Screenshots (MANDATORY — ALL Bug Types)

**NEVER SKIP BROWSER TESTING.** Even "pure backend" changes affect SSR rendering, page loading, and the overall application. If there is no UI change, still test that the app loads without crashing.

1. **Ensure the dev server is running:**
   - Start it in background: `npm run dev` (or `npm run preview` for production build)
   - Wait for the server to be ready (poll the port until it accepts connections)
   - If the server fails to start → **this is a runtime crash** → go back to Step 5
   - If the server starts but pages return 500 errors → this is a runtime crash → go back to Step 4

2. **Write a UAT test file** to `bugs/uat-tests/BUG-XXX-uat.spec.ts`:
   - Must navigate to the affected page(s)
   - Must take screenshots (`page.screenshot({ path: 'test-results/bugXXX-<description>.png', fullPage: true })`)
   - Must verify page content loaded correctly (not blank, not error page)
   - Must verify the bug-specific fix (e.g., setting is applied, feature works)
   - Must pass within a reasonable timeout (60s per test max)

3. **Run the UAT test:**
   ```
   cd ~/projects/<project>
   npx playwright test bugs/uat-tests/BUG-XXX-uat.spec.ts
   ```
   If `playwright` is not available, use `npx playwright test`.

4. **Verify screenshots** (main session MUST inspect them):
   - Screenshots prove the page rendered without crashes
   - If screenshots show error messages, blank pages, or 500 errors → test FAILED
   - Save passed screenshots to `bugs/uat-results/BUG-XXX-uat-screenshot.png`

5. **If UAT fails:**
   - ❌ **DO NOT DECLARE THE BUG FIXED**
   - Save failed screenshots as `bugs/uat-results/BUG-XXX-failed.png`
   - Write a `bugs/uat-results/BUG-XXX-failed.md` with the failure reason and screenshots
   - Go back to Step 4 (fix again)
   - Max 3 fix-attempts per bug before escalation

6. **If UAT passes:**
   - ✅ Proceed to Step 7 (Commit).

### Step 7: Commit to GitHub

Only reached AFTER build passes AND UAT passes.

1. Git operations in the project directory:
   ```
   cd ~/projects/<project>
   git add -A
   git commit -m "Fix BUG-XXX: <title>"
   git push origin main
   ```
   (Note: use `main` not `master` — check the project's default branch first)

2. If git fails (e.g., branch mismatch, credentials) → report error to user.

3. Update the bug file:
   - Change Status from `[ ] New` / `[~] Reopened` to `[x] Done`
   - Fill Eva2 Notes section:
     | Field | Value |
     |-------|-------|
     | **Fix commit** | `<commit hash>` |
     | **Resolution** | Brief description of what was fixed |
     | **Time spent** | Approximate time |
   - If fix had retry attempts, note them in Eva2 Notes.

### Step 8: Final Report

After ALL bugs have been processed:

1. Generate a summary report in chat:
   ```
   🐛 Bug Fixing Session Complete

   Project: <project-name>

   Results:
   ✅ BUG-XXX - Fixed & UAT Passed
   ✅ BUG-YYY - Fixed & UAT Passed
   ❌ BUG-ZZZ - Failed after 3 attempts (escalated)

   Commits pushed to GitHub.
   ```

2. **MANDATORY: Remind user to do their own UAT:**
   ```
   All automated fixes are done. Please do your own UAT test on the latest build and let me know if you find any remaining issues.
   ```

3. Update the project's `bugs/` README or a tracking file if one exists.

## Bug File Markup Convention

After processing, a completed bug file should look like:

```markdown
| Field | Value |
|-------|-------|
| **Status** | `[x] Done` |
| ...

---
## Eva2 Notes

| Field | Value |
|-------|-------|
| **Fix commit** | `abc1234` |
| **Resolution** | Fixed [component] — [root cause and fix] |
| **Time spent** | ~15 min |
| **UAT Status** | ✅ PASS |
```

## Error Handling

| Situation | Action |
|-----------|--------|
| Bug file missing/corrupt | Report to user, skip bug |
| Bug has no test case file | Generate test cases on the fly (Step 2) |
| Fix agent kills itself | Restart with same slice (max 3 times) |
| Fix modifies files outside scope | Revert, re-scope, restart |
| Build fails after fix | **Class 1 crash** — do NOT commit, fix the build first |
| Dev server won't start | Runtime crash — go back to fix (Step 4) |
| Dev server returns 500 on pages | Runtime crash — go back to fix (Step 4) |
| UAT test fails | Do NOT declare fixed — go back to Step 4 |
| UAT timeout (>10 min) | Kill agent, retry once, then escalate |
| Git push fails | Report error to user, continue to next bug |
| User says "priority bug" | Process that bug immediately, queue others |

## NEVER Rules (Violating These Is Not Acceptable)

1. **NEVER declare a bug fixed before build passes.**
2. **NEVER declare a bug fixed before UAT passes with screenshots.**
3. **NEVER skip browser testing for "backend-only" bugs** — SSR crashes are real.
4. **NEVER commit code that crashes the dev server or returns 500.**
5. **NEVER skip writing the UAT test file.**
6. **NEVER skip taking and verifying screenshots.**
7. **NEVER re-use a failed fix attempt without analyzing what went wrong.**
8. **NEVER exceed 3 fix attempts per bug** — escalate to the user.

## Efficiency Rules

- **Batch independent bugs**: If two bugs affect completely different components and won't conflict, fix in parallel.
- **Sequential for same file**: If two bugs touch the same file/component, fix one at a time.
- **Commit per bug**: One commit per bug for clean history.
- **Push in batch**: Push all commits together after final verification.
- **Cron watchdog**: The subagent-watchdog cron must be active before long fix sessions.
- **Max 3 per-slice retries** before escalation.

## Invocation

The skill is triggered when the user says any of:
- "fix bugs"
- "bug fixing"
- "I completed [project] test" / "[project] test is done"
- "test [project]"
- "please fix [project] bugs"
- "bugs are still existing" (UAT failure from previous round)
