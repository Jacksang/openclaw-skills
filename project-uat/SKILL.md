# Project UAT — End-to-End User Acceptance Testing

A battle-tested UAT workflow for delivering projects to customers: test case generation, validation gates, automated execution, and bug resolution.

## When to Use

After all implementation and integration testing is complete. Prerequisites:
- User stories with acceptance criteria (`plan/E0X-*-stories.md`)
- UI design specifications (`uidesign/PAGE_*.md`)
- Running application (backend + frontend)
- Integration test reports (all passing)

---

## UAT Phase Structure

```
1. Test Case Generation (epic-by-epic)
2. Validation Gate (gpt5.5 QA lead)
3. Fix → Re-validate Cycle (until PASS)
4. Split into Manageable Files
5. Automated Execution (flash model)
6. Bug Fix Cycle
7. Final Sign-off
```

---

## 1. UAT Test Case Generation

### DO NOT write all UAT cases in one call.

Generate epic by epic, one tester agent call per epic:

```
Spawning order (one agent per epic, flash model):
  1. UAT epic E01 (Auth & Platform) → tests/uat/E01-role-uat.md
  2. UAT epic E02 (Sessions) → tests/uat/E02-role-uat.md
  3. UAT epic E03 (Imaging) → tests/uat/E03-role-uat.md
  4. UAT epic E04 (Reports) → tests/uat/E04-role-uat.md
  5. UAT epic E05 (Commerce) → tests/uat/E05-role-uat.md
  6. UAT epic E06 (Referrals) → tests/uat/E06-role-uat.md
  7. UAT epic E07 (System) → tests/uat/E07-system-uat.md
  8. Cross-role E2E workflows → tests/uat/CR-cross-role-e2e-uat.md
  9. UI compliance checklist → tests/uat/UI-compliance-checklist.md
```

### File Structure

One file per epic-role user story. Self-contained:

```markdown
# ELY2 UAT — E01 Prospect: Auth & Platform
**Version:** 1.0 | **Date:** YYYY-MM-DD
**Role:** Prospect | **Epic:** E01
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

### UAT Pass Criteria
a) UI matches design specifications (layout, colors, typography)
b) No emojis in UI, all images visible and properly sized
c) End-to-end flows are smooth, responsive, clear action feedback
d) Every clickable element is responsive, missing content triggers visible error

---

## 2. Validation Gate

Spawn QA Lead validator on premium model (gpt5.5):

### Criteria (per file, score 1-5)
1. **Validity** — Traces to user stories. Tests acceptance criteria.
2. **Completeness** — All scenarios: positive, negative, edge, empty, error, loading. All roles.
3. **Accuracy** — Expected results match system. No HTTP codes in browser steps. Correct field names.
4. **Practicability** — Concrete steps. Clear setup. Reasonable duration.

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

## 4. Split Files

If generated as single file, split into one per epic-role. Makes files manageable for human testers.

---

## 5. Automated Execution (AI Round 1)

Spawn UAT executor on fast model (deepseek-v4-flash):

### Can Verify
- API endpoints exist and return correct shapes
- Frontend HTML loads CSS/JS files
- DOM elements exist with correct IDs/classes
- Error responses follow standard envelope
- No emojis in rendered UI
- Legal pages exist

### Cannot Verify (requires human)
- Visual layout matches design
- Colors/typography/spacing pixel-perfect
- Animations and transitions
- Real browser rendering
- Touch/mobile interactions

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

## 6. Bug Fix Cycle

```
1. Categorize: Backend or Frontend?
2. Spawn fix agent
3. Fix + verify + commit + push
4. Mark bug resolved
```
Backend and frontend bugs can be fixed in parallel.

### Image Assets
If UI spec references missing images: use gpt-image model to generate logo/hero/icons.

---

## 7. Final Sign-Off

### AI Round 1 Complete
- [ ] All files executed
- [ ] Execution report published
- [ ] All bugs fixed and verified
- [ ] Zero emojis in codebase
- [ ] Build succeeds

### Human Round 2 Ready
- [ ] Environment running
- [ ] Files organized by epic-role
- [ ] Test data seeded
- [ ] Sign-off sheets ready

---

## Retrospective: Lessons Learned

### ✅ Right
1. **Epic-by-epic generation** — avoids timeout, keeps files manageable
2. **Validation gate** — caught real gaps (camera capture, E07, HTTP codes)
3. **Two-round cycle** — v1.0 FAIL → fix → v1.1 PASS
4. **Automated execution** — found 9 bugs integration tests missed
5. **Emoji detection** — systematic grep caught what manual review missed
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
| Validator on weak model | gpt5.5 for validation, flash for generation |
| Only AI execution | Human Round 2 mandatory |
| Fixing bugs without reports | File bugs with reproduction steps |
