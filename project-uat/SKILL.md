---
name: project-uat
description: "End-to-end User Acceptance Testing workflow: epic-by-epic UAT case generation, validation gate with a strong model, automated AI execution round, human execution round, bug fix cycle, and final customer sign-off. Use when implementation and integration testing are complete and the project needs UAT, acceptance testing, or customer delivery sign-off."
allowed-tools: read,write,edit,exec
user-invocable: true
---

# Project UAT — End-to-End User Acceptance Testing

A battle-tested UAT workflow for delivering projects to customers: test case generation, validation gates, automated execution, bug resolution, and final sign-off.

## Position in Skill Chain

```
project-planner → project-quoter → ⏸️ CUSTOMER DECISION → project-implementation → project-uat → project-delivery
```

## Entry Gate (Must Match project-implementation's Exit Gate)

- [ ] All integration test reports published (`plan/TEST_REPORT_E0X.md`), all passing
- [ ] All critical/high bugs resolved and verified
- [ ] Build succeeds; application runs (backend + frontend)
- [ ] User stories with acceptance criteria (`plan/E0X-[role]-stories.md`)
- [ ] UI design specifications (`uidesign/PAGE_*.md`)

**Model rule (same as planning validation):** generate UAT cases with a fast/cheap model; validate with a strong model. Validator model ≠ generator model.

---

## UAT Phase Structure

```
1. Test Case Generation (epic-by-epic)
2. Validation Gate (strong-model QA lead)
3. Fix → Re-validate Cycle (until PASS)
4. Automated Execution (AI Round 1, fast model)
5. Bug Fix Cycle
6. Human Execution (Round 2)
7. Final Sign-off → hand off to project-delivery
```

---

## 1. UAT Test Case Generation

### DO NOT write all UAT cases in one call.

Generate epic by epic, one tester agent call per epic (fast model). For a project with epics E01…E0N:

```
Spawning order (one agent per epic):
  1..N. UAT epic E0X → tests/uat/E0X-[role]-uat.md   (one call per epic, in order)
  N+1.  Cross-role E2E workflows → tests/uat/CR-cross-role-e2e-uat.md
  N+2.  UI compliance checklist → tests/uat/UI-compliance-checklist.md
```

This prevents agent timeouts, allows incremental review, and keeps each call focused on one domain. **Never generate a single monolithic UAT file.** (If you inherit one from a previous attempt, split it into one file per epic-role before proceeding.)

### File Structure

One file per epic-role user story. Self-contained:

```markdown
# [Project] UAT — E0X [Role]: [Epic Name]
**Version:** 1.0 | **Date:** YYYY-MM-DD
**Role:** [Role] | **Epic:** E0X
**Duration:** ~15 min

## Pre-Test Setup
| # | Step | Expected | [ ] |

## Test Steps
| # | Step | Expected | [ ] |

## Error States
| # | Step | Expected | [ ] |

## UI Design Checklist
| Check | Criteria | [ ] |

## Sign-Off
| Tester | Date | Result | Issues |
```

Always include a duration estimate — it helps human testers plan.

### UAT Pass Criteria
a) UI matches design specifications (layout, colors, typography)
b) No emojis in rendered UI or user-facing strings; all images visible and properly sized
c) End-to-end flows are smooth, responsive, clear action feedback
d) Every clickable element is responsive, missing content triggers visible error
e) Accessibility basics: full keyboard navigation, visible focus states, sufficient color contrast, alt text on images
f) No unrelated third-party content: no ads, promotional links, "Powered by" badges, vendor watermarks, tracking hooks, or template-leftover links anywhere in the UI, emails, or generated documents

The UI compliance checklist must include the accessibility items from (e) and the NFR targets from `plan/ARCHITECTURE.md`.

---

## 2. Validation Gate

Spawn a QA Lead validator on a strong model (must differ from the generation model):

### Criteria (per file, score 1-5)
1. **Validity** — Traces to user stories. Tests acceptance criteria.
2. **Completeness** — All scenarios: positive, negative, edge, empty, error, loading. All roles.
3. **Accuracy** — Expected results match system. No HTTP codes in browser steps. Correct field names.
4. **Practicability** — Concrete steps. Clear setup. Reasonable duration.

The validator must cross-reference all sources: user stories, design specs, and API docs — not just the UAT files themselves.

### PASS Rules
- PASS: All 4 category AVERAGES ≥ 4.0
- FAIL minor: 1-2 categories average 3.0-3.9
- FAIL major: Any category average < 3.0

---

## 3. Fix → Re-validate Cycle

```
LOOP:
  1. Validator scores files
  2. If PASS → exit loop
  3. Tester agent fixes specific gaps from validation report
  4. Go to step 1
```
Stop after 3 rounds — escalate to human.

---

## 4. Automated Execution (AI Round 1)

Spawn a UAT executor on a fast model:

### Can Verify
- API endpoints exist and return correct shapes
- Frontend HTML loads CSS/JS files
- DOM elements exist with correct IDs/classes
- Error responses follow standard envelope
- No emojis in rendered UI
- **No unrelated third-party content**: grep rendered pages, templates, and email templates for external URLs, script tags, and iframe/pixel embeds — every hit must trace to the design spec or architecture doc; flag ads, promo links, vendor credits, and tracking hooks as bugs
- Legal pages exist
- **With browser automation (if available):** take a screenshot of each page in each state (desktop + mobile viewport), compare against the mockups in `uidesign/assets/`, and flag gross layout/color deviations — this narrows what humans must check in Round 2

### Cannot Verify (requires human)
- Pixel-perfect colors/typography/spacing judgments
- Animations and transitions
- Touch/mobile interactions on real devices
- Overall feel: smoothness, clarity of feedback

Filter cosmetic non-issues before filing (e.g. an empty directory is not a bug).

### Execution Report
```markdown
# UAT Execution Report
## Summary
| Epic | File | Steps | Pass | Fail |

## Bugs Filed
| Bug ID | Severity | Source | Issue |

## Detailed Results
| File | Step | Expected | Actual | Verdict |
```

---

## 5. Bug Fix Cycle

**Triage first** using the canon in project-implementation ("Bug vs Change Request Triage"): UAT is where "bug vs feature" disputes peak. A deviation from signed stories/tests/designs is a defect (fix free); "I'd like it to also do X" is a change request (route to the quoter's change-order process — do not fix it inside UAT).

For confirmed defects:

```
1. Categorize: Backend or Frontend?
2. Spawn fix agent using project-implementation's Bug Fix Agent Template
   (regression test first, per the tdd-workflow skill)
3. Fix + verify + commit + push
4. Mark bug resolved
```
Backend and frontend bugs can be fixed in parallel (different file trees — no lock contention).

Use the bug file format from the **project-implementation** skill (`bugs/BUG-XXX-title.md`, severity 🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low) so the bug trail is consistent across phases.

### Image Assets
If UI spec references missing images: use the available image-generation tool to generate logo/hero/icons.

---

## 6. Human Execution (Round 2 — MANDATORY)

AI cannot replace human eyes for visual design. Before handing over:

- [ ] Environment running and reachable by the tester
- [ ] UAT files organized by epic-role
- [ ] Test data seeded
- [ ] Sign-off sheets ready

**Browser/device matrix** (matching the planner's desktop + mobile layouts):

| | Desktop | Mobile viewport |
|--|---------|-----------------|
| Chrome | required | required |
| Safari | required | required (iOS if available) |

Then the human tester executes the UAT scripts in a real browser, checking each step's `[ ]` box and recording issues in each file's Sign-Off table. Bugs found here are triaged (Section 5) — defects go through the fix cycle, then the affected scripts are re-run.

---

## 7. Final Sign-Off → Hand Off to project-delivery

### AI Round 1 Complete
- [ ] All files executed
- [ ] Execution report published
- [ ] All bugs fixed and verified
- [ ] Zero emojis in rendered UI / user-facing strings
- [ ] Build succeeds

### Human Round 2 Complete
- [ ] Every UAT file's Sign-Off table filled in
- [ ] All Round 2 bugs resolved and re-tested
- [ ] Customer acceptance recorded in `plan/UAT_SIGNOFF.md` (date, signatory, outstanding items if any)

UAT sign-off means the build is accepted — it is not yet live. Invoke the **project-delivery** skill for deployment, security verification, handover, training, warranty, and the final payment milestone. Project close (calibration logging, retrospective) happens there, at the true end of the project.

---

## Retrospective: Lessons Learned

### ✅ Right
1. **Epic-by-epic generation** — avoids timeout, keeps files manageable
2. **Validation gate** — caught real gaps (missing device-capture flows, a whole missed epic, HTTP codes in browser steps)
3. **Two-round cycle** — v1.0 FAIL → fix → v1.1 PASS
4. **Automated execution** — found 9 bugs integration tests missed
5. **Emoji detection** — systematic grep of rendered output caught what manual review missed
6. **Parallel fixes** — backend + frontend simultaneously

### ❌ Improve
1. **Start epic-by-epic from the beginning** — don't create monolithic files
2. **Validator must cross-reference all sources** — user stories, design specs, API docs
3. **AI cannot replace human eyes** — Round 2 mandatory for visual design
4. **Filter cosmetic issues** — empty dir ≠ bug
5. **Always include duration estimates** — helps testers plan

---

## Antipatterns

| Antipattern | Fix |
|-------------|-----|
| One massive UAT file | Generate epic-by-epic |
| Skipping validation | Always validate before execution |
| Validator on weak model | Strong model for validation, fast model for generation |
| Only AI execution | Human Round 2 mandatory |
| Fixing bugs without reports | File bugs with reproduction steps |
| Building feature requests during UAT | Triage first — new scope goes to the change-order process |
| Stopping at "ready for human testing" | UAT ends at recorded customer sign-off, then hands off to project-delivery |
