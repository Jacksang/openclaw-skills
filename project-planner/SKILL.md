---
name: project-planner
description: "Plan software projects from draft scope → role-based user stories → behavior tests → UI/UX design. Story-first, persona-driven, zero-code planning phase."
allowed-tools: read,write,edit,exec,image_generate
user-invocable: true
---

# Project Planner

End-to-end project planning methodology. Start from a customer's draft scope and produce validated user stories, test cases, UI/UX designs, and visual mockups — all before writing a single line of code.

## Philosophy

**Story-first, persona-driven, epic-by-epic.** Every feature is examined from every role's perspective before implementation begins. No code until the plan is complete. Work one epic at a time through all stages — this keeps context tight, reduces token waste, and ensures deep focus on each epic before moving to the next.

## The Workflow: Epic-by-Epic, Stage-by-Stage

```
GLOBAL (once):    Stage 1 (Draft Scope) → Stage 2 (Roles)

PER EPIC (loop):  Stage 3 (Stories) → Stage 4 (Tests) → Stage 5 (UI/UX)
                         ↓
                  Stage 7 (Validate) → FIX → RE-VALIDATE → PASS ✓
                         ↓
                  Next epic

GLOBAL (after):   Stage 6 (Images) — generate priority mockups from validated designs
```

**Why epic-by-epic:**
- Context stays tight: one epic's files at a time, not 20+ files across all epics
- Validation is deeper: agent reads 4-10 files per epic, not 40+
- Lessons compound: E01 fixes inform E02's stories before they're even validated
- Token efficient: no re-reading all files per validation run — ~80% less context waste
- Proven on ELY2: batch approach needed 3 rounds per epic; epic-first would need 1-2

## Stages 1-2: Global (Run Once)

### Stage 1: Draft Scope

**Input:** Customer provides `plan/Draft scope.md` — a feature catalog with business objectives, functional requirements, acceptance criteria.

**Action:** Read and understand. Do not edit the draft scope. It belongs to the customer.

**Output:** Confirmed understanding of all epics, phases, and features.

### Stage 2: Role Identification

**Goal:** Extract every user role from the draft scope.

**Process:**
- Read every epic and every functional requirement.
- Extract all mentioned actors: Prospect, Customer, Therapist, Admin, System, etc.
- Document each role with evidence quotes from the scope.
- System is always a role — automated background processes.

**Output:** Clear role list with evidence from draft scope.

## Stages 3-7: Per-Epic Loop (Repeat for E01, E02, E03...)

Work through each epic completely before starting the next. This means: write stories for E01 → write tests for E01 → design pages for E01 → validate E01 → fix any gaps → E01 passes. Only then start E02.

### Stage 3: Role-Feature User Stories

**Goal:** For each epic, create one story file per role.

**File naming:** `plan/E0X-[role]-stories.md`

**Format:**
```
# E0X — User Stories: [Role]
## US-E0X-ROLENAME-01: Story Title
As a **[Role]**, I need to **[action]**, so that **[outcome]**.

**Acceptance Criteria:**
- AC-01: ...
- AC-02: ...
```

**Rules:**
- One file per role per epic. Roles not participating in that epic get no file.
- Stories are decoupled — each story stands alone.
- Every story starts with "As a [role], I need to..."
- 3–7 acceptance criteria per story, concrete and testable.
- Cover what a role CAN do (not implementation details).
- Edge cases: "what if the system is down?", "what if data is missing?"

### Stage 4: Behavior Test Cases

**Goal:** For each story file, create a corresponding test file with positive and negative test cases.

**File naming:** `tests/E0X-[role]-tests.md`

**Format:**
```
## US-E0X-ROLENAME-01: Story Title
### ✅ Positive Behavior Tests
**TEST-ID-P1: Scenario name**
| Field | Value |
|-------|-------|
| Scenario | [description] |
| Pre-condition | [required state] |
| Steps | 1. ... 2. ... |
| Expected | • Result 1 • Result 2 |

### ❌ Negative Behavior Tests
**TEST-ID-N1: Scenario name**
...
```

**Rules:**
- At least 1 positive + 1 negative test per story.
- Positive: happy path, normal workflow.
- Negative: errors, edge cases, boundary conditions, security bypasses, timeouts.
- Tests reference exact HTTP status codes, response fields, error messages.
- No implementation detail — test behavior, not code.

### Stage 5: UI/UX Design

**Goal:** Create design system spec and text-based wireframes covering every UI-visible story.

**Process:**
1. Create `uidesign/DESIGN_SYSTEM.md` — colors, typography, components, spacing, accessibility.
2. For each role, find the appropriate page for each story:
   - If a suitable page exists → add the story's UI to that page.
   - If no page exists → create a new page.
   - Sub-pages and modals are documented within their parent page.
3. Create shared component specs (`uidesign/COMPONENTS_SHARED.md`) for modals, toasts, empty states, etc.
4. Create `uidesign/INDEX.md` mapping every story to its page.

**Page file format:**
```
# PAGE: [Name]
**Page ID:** PAGE_XXX
**Route:** #/xxx
**Role:** [role]
**User Stories:** [story IDs]
**Layout:** Desktop + Mobile (or Desktop only for admin)

## Desktop Layout (text-based wireframe using ASCII art)
## Mobile Layout (if applicable)
## Component Details (spec table per element)
## Sub-Components (modals, dropdowns)
## States (normal, loading, empty, error, success)
## Navigation (from → action → to)
```

**Design principles:**
- Text-based wireframes using ASCII art — no images yet.
- Admin pages: desktop primary. Customer/therapist pages: desktop + mobile.
- Every state covered: normal, loading, empty, error, success.
- Use the design system tokens consistently (colors, spacing, fonts).

## Stage 6: Image Generation (After All Epics Validated)

**Goal:** Generate visual mockups for customer presentation from validated page designs.

**Process:**
1. Create `uidesign/assets/` folder.
2. Generate priority pages first (login, admin dashboard, therapist session, patient dashboard).
3. Use `image_generate` with OpenAI model, 1536×1024 size.
4. Each image prompt must include: exact colors, layout description, component positions, brand name.
5. Save each image to assets folder with descriptive filename.
6. Create `uidesign/assets/README.md` inventory.
7. Pace generations — one at a time, respect rate limits.

**Prompt template:**
```
A clean [page name] UI mockup for "[platform name]".
[detailed layout description with positions].
[color specifications with hex codes].
Clean minimal design, [font] font, no emojis.
Professional [industry] feel.
```

## Reference Implementation

Canonical example: `~/projects/ely2/` — ELY Wellness Platform v2
- 7 epics → 5 roles → 68 user stories → 141 test cases → 16 pages → 7 mockups
- Full traceability from Draft scope to visual mockup

### ELY2 Lessons (Baked into This Skill)

1. **Epic-by-epic beats batch**: Batching all 7 epics through each stage caused context overload. The validation agent had to read 40+ files per run, missed issues, and needed 3 rounds per epic. Epic-by-epic keeps files-per-run to 4-10.
2. **Different model for validation**: Planner model missed 3 issues; validation model found 50+. Model diversity is critical.
3. **Validate before images**: Don't generate mockups until designs are validated — avoids regenerating fixed designs.
4. **Draft scope is read-only**: The customer's document has its own vocabulary (e.g., "in-progress"). Don't fight it — document your convention in story files and tests.
5. **Status vocab centralize early**: E02 burned 3 rounds just on `in_progress` vs `in-progress`. Define canonical terms in the first epic's stories and carry them forward.

### Stage 7: Plan Validation (Quality Gate — Different Model)

**Goal:** Independent, adversarial review of all planning artifacts using a **different, stronger LLM** before implementation begins. The validation model must NOT be the same as the planning/execution model.

**Why a different model:**
- The planning model has bias — it wrote the artifacts.
- A different model brings fresh eyes, finds gaps the planner missed.
- The ELY2 experience proved this: the same-model validation found 3 issues; a different-model staged validation found 50+ issues.

**Model pairing (recommended):**
- Planning: `openai/gpt-5.5` or current best model
- Validation: `openai/gpt-5.5` (if planning on deepseek) or `deepseek/deepseek-v4-pro` (if planning on openai)
- Rule: **planner model ≠ validation model**

**Process:**
1. Spawn a validation sub-agent with a different model than the planner.
2. **Validate one epic at a time** — pass only the draft scope + that epic's story files + test files + UI pages.
3. This controls context and allows deeper per-epic analysis.
4. Validation criteria: validity, completeness, accuracy, practicality.
5. Agent outputs: PASS/FAIL per criterion + specific improvement suggestions.
6. Planner agent makes modifications based on feedback.
7. Re-run validation until all criteria pass — E01 took 2 rounds, E02 took 3 rounds.
8. Milestone achieved when ALL epics pass validation.
9. **If validation agent hits model rate limits**, the planner can self-audit against the same criteria as fallback, but this is NOT the preferred path.

**Validation checklist per epic:**
- Every role in draft scope has stories in the plan.
- Every story has acceptance criteria.
- Every story has positive + negative test cases.
- Every UI-visible story maps to a page design.
- Page designs include all states (normal, loading, empty, error).
- API contract assumptions are explicit and traceable.
- Entry points exist for every role (no orphaned personas).
- Edge cases covered (timeouts, failures, boundary values).

**Validation prompt template:**
```
You are a senior solution architect reviewing a project plan. 
Read the Draft scope, all story files, all test files, and all page designs.
Evaluate each epic on:
1. VALIDITY — Are stories logically correct? Do flows make sense?
2. COMPLETENESS — Are all roles covered? All edge cases? All states?
3. ACCURACY — Do page designs match story requirements? API assumptions correct?
4. PRACTICALITY — Can these be implemented? Any obvious blockers?

For each criterion, output PASS or FAIL with specific suggestions.
```

## Execution Rules

1. Never edit the customer's Draft scope.
2. Always write stories per role, not per feature.
3. System is always a role — automated processes need stories too.
4. Every user-visible story must map to a page or sub-component.
5. No code during planning. This is the blueprint phase.
6. Commit after each epic's validation pass. Each commit is a checkpoint.
7. Work epic-by-epic, stage-by-stage: E01 complete before starting E02.
8. Stage 7 (Validation) is mandatory before implementation. Use a DIFFERENT model than the planner.
9. Image generation runs after ALL epics validated — designs must be final first.
