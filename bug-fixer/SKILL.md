# Bug Fixer Skill

## Overview

This skill provides a rigorous, multi-step workflow for fixing bugs submitted via the Bug Tracker (http://localhost:3003). It eliminates the ping-pong cycle of "I said it was fixed but it wasn't" by enforcing:

1. **Test-case-first**: Every bug is reviewed by a test agent before any code is written.
2. **Verification loop**: Fixes are automatically UAT-tested with browser screenshots.
3. **Retry on failure**: Failed UAT spawns a new fix cycle; never report "done" until tests pass.
4. **Formal sign-off**: Final report with explicit PASS/FAIL and a reminder for human UAT.

## Workflow

### Step 0: Prerequisites Check

- Bug Tracker service running on `http://localhost:3003` (project at `bug-tracker/`)
- Target project directory exists at `~/.openclaw/workspace/projects/<project>` or `~/projects/<project>`
- Project has `bugs/` folder with bug files in format `BUG-###-title.md`
- Bug files use the template (Status: `[ ] New` or `[~] Reopened` → needs fixing)

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

Based on the bug description (frontend, backend, or both), decide:

- **Frontend bug** → requires browser testing/UAT with screenshots after fix.
- **Backend bug** → API-level test is sufficient (no screenshots needed).
- **Both** → run frontend + backend fix agents separately.

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

### Step 5: Commit to GitHub

After ALL fix slices for the current bug are done:

1. Git operations in the project directory:
   ```
   cd ~/projects/<project>  # or ~/.openclaw/workspace/projects/<project>
   git add -A
   git commit -m "Fix BUG-XXX: <title>"
   git push origin master
   ```
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

### Step 6: UAT Test (Frontend Bugs Only)

Skip for pure backend bugs (go to Step 8).

1. **Ensure the dev server is running** for the project:
   - If not running, start it in background (e.g., `npm run dev`).
   - Wait for server to be ready (poll port until accepting connections).

2. **Spawn UAT test sub-agent** (`mode="run"`, isolated, 10 min timeout):
   - Agent reads the bug file + test case file.
   - Agent uses browser (playwright/puppeteer or headless Chrome) to:
     - Navigate to the affected page/route.
     - Follow steps to reproduce and verify bug is fixed.
     - Take screenshots before/after where relevant.
     - Write results to `bugs/uat-results/BUG-XXX-uat-report.md` with embedded screenshots.
   - Agent returns PASS or FAIL with clear evidence.

3. **Monitor UAT agent** — same watchdog approach.

### Step 7: UAT Result Handling

**If PASS:**
- Update bug file: append `**UAT Status:** ✅ PASS` to Eva2 Notes.
- Move to the next pending bug (back to Step 2).

**If FAIL:**
- Update bug file: append `**UAT Status:** ❌ FAIL` with the failure reason.
- **Increment fix attempt counter** for this bug.
- If attempts < 3 → go back to Step 4 (fix again).
- If attempts >= 3 → escalate: report to user with details, mark bug as `[!] Escalated`, stop processing this bug and continue to next.

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

2. **Important: Remind user to do their own UAT:**
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
| Git push fails | Report error to user, continue to next bug |
| Server won't start for UAT | Report error, skip UAT, notify user |
| UAT timeout (>10 min) | Kill agent, retry once, then escalate |
| User says "priority bug" | Process that bug immediately, queue others |

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
