---
name: project-delivery
description: "Go-live and handover workflow: production deployment, security and vulnerability verification, monitoring/backup/rollback setup, handover documentation, customer training, warranty terms, final payment trigger, and project close. Use when UAT is signed off and the project needs deployment, go-live, delivery, handover, or post-launch support setup."
allowed-tools: read,write,edit,exec
user-invocable: true
---

# Project Delivery — Go-Live, Handover & Close

The final mile: take a UAT-approved build to production, prove it's secure and operable, hand it over, and close the commercial loop. This is where the go-live payment milestone (typically the final 25%) is earned.

## Position in Skill Chain

```
project-planner → project-quoter → ⏸️ SIGN → project-implementation → project-uat → project-delivery
```

## Entry Gate

- [ ] UAT sign-off recorded (`plan/UAT_SIGNOFF.md`)
- [ ] All UAT bugs resolved; build green; all commits pushed
- [ ] `plan/ARCHITECTURE.md` specifies the deployment target
- [ ] Signed proposal at hand — its tier defines what delivery includes (training, support period, add-ons)

---

## Stage 1: Production Pre-Flight

- Provision production environment per `ARCHITECTURE.md` (compute, database, storage)
- Domain + DNS + TLS certificate (verify auto-renewal)
- Production secrets: generated fresh, stored in the platform's secret manager — never copied from dev, never committed
- Run all migrations against production DB; verify schema matches dev
- Seed production reference data (not test data — no demo users left behind)
- Configure CORS, allowed hosts, and environment flags for production

**Output:** `plan/DEPLOYMENT_RUNBOOK.md` — every step recorded so the deployment is repeatable.

---

## Stage 2: Security & Vulnerability Verification (MANDATORY GATE)

Run against the production (or production-identical staging) environment. All items must pass before go-live:

### 2.1 Static & Dependency Checks
- [ ] Dependency audit clean of critical/high (`npm audit --omit=dev` or stack equivalent)
- [ ] Secrets scan on the full repo history (e.g. gitleaks) — no credentials, keys, or tokens committed
- [ ] Debug endpoints, verbose error output, and default credentials removed
- [ ] **Third-party content sweep**: grep the production build, email templates, and handover docs for external URLs and embeds — no ads, promotional links, vendor "powered by" credits, template leftovers, or tracking hooks the customer didn't approve. Customer-facing surfaces carry the customer's brand only

### 2.2 Runtime Checks
- [ ] **Authorization matrix re-run in production config**: every endpoint × every role, including cross-tenant checks ("role A cannot read role B's data")
- [ ] Security headers present (CSP, HSTS, X-Frame-Options, X-Content-Type-Options)
- [ ] Rate limiting active on auth and other abuse-prone endpoints
- [ ] TLS only — HTTP redirects to HTTPS; no mixed content
- [ ] Session/token expiry and refresh behave per `ARCHITECTURE.md`
- [ ] File uploads (if any): type/size validated, stored outside web root

### 2.3 Vulnerability Scan
- [ ] Run a baseline automated scan against staging/production (e.g. OWASP ZAP baseline scan); triage every finding — fix or document acceptance with reason
- [ ] Walk the OWASP Top 10 as a checklist against the codebase; record results

**Formal penetration test** is a priced add-on (offer via project-quoter), not part of standard delivery — but the baseline scan above is always mandatory.

**Output:** `plan/SECURITY_CHECKLIST.md` with pass/fail per item. Any failed item blocks go-live.

---

## Stage 3: Deploy & Production Smoke Test

- Deploy via the CI/CD pipeline (not manual copy) — this proves the pipeline works
- Run the production smoke test:
  - [ ] Health endpoint responds
  - [ ] One real end-to-end flow per role (login → core action → logout)
  - [ ] Emails/notifications actually send from production
  - [ ] Payments (if any) work in live mode with a real minimal transaction, then refund
- Verify rollback: deploy previous version, confirm recovery, re-deploy current

---

## Stage 4: Operability

- [ ] Monitoring: uptime check + error rate alerting wired to a channel someone reads
- [ ] Centralized logs retained ≥ 30 days
- [ ] Automated database backups scheduled; **one restore actually tested** (an untested backup is not a backup)
- [ ] Rollback procedure documented in the runbook
- [ ] Infrastructure costs confirmed against the proposal's estimate — flag overruns to the customer now, not on their first invoice

---

## Stage 5: Handover Documentation

Produce in `docs/`:

| Document | Audience | Contents |
|----------|----------|----------|
| `ADMIN_GUIDE.md` | Customer admin | User management, configuration, common operations |
| `USER_GUIDE.md` | End users | Per-role walkthroughs of the main flows (reuse UAT scripts — they are already step-by-step) |
| `API_REFERENCE.md` | Future developers | Endpoints, auth, error envelope (generate from code where possible) |
| `OPERATIONS.md` | Whoever runs it | Runbook, monitoring, backup/restore, rollback, incident contacts |

---

## Stage 6: Training (If Sold)

If the signed tier includes training (e.g. Premium):
- Prepare a session agenda from the USER_GUIDE flows
- Deliver live or recorded walkthrough per role
- Record attendance and Q&A in `plan/TRAINING_LOG.md` — this is contractual evidence of delivery

---

## Stage 7: Warranty & Post-Launch Request Triage

Define in `plan/WARRANTY_TERMS.md` (and confirm it matches the proposal):
- Warranty period (e.g. 30–90 days) and what it covers
- Response time targets by severity
- Support channel

**Triage every post-launch request using the canon defined in the project-implementation skill ("Bug vs Change Request Triage"):**
- **Defect** (deviates from signed stories/tests/designs) → fixed free under warranty
- **Specification gap** → tiny: goodwill fix + update the artifact; otherwise → change request
- **New feature** → change request → mini-pipeline (delta epic through planner → quoter change order → implementation phase → targeted UAT). Never absorb silently.

---

## Stage 8: Final Payment & Project Close

- [ ] Go-live confirmed by customer in writing → **trigger the final payment milestone**
- [ ] Log actual hours per module to `plan/ACTUAL_VS_ESTIMATE.md` and roll up into `project-quoter/references/calibration-log.md` (tier chosen, negotiation notes, estimate accuracy)
- [ ] Write the project retrospective — what worked and what didn't become future skill improvements
- [ ] Archive: repo tagged with release version, deliverables zipped/stored, credentials handed over and rotated out of your possession

---

## Antipatterns

| Antipattern | Fix |
|-------------|-----|
| "It passed UAT, just deploy it" | Production config ≠ dev config; run Stages 1–3 fully |
| Skipping the vulnerability scan | Baseline scan is mandatory; pen-test is the optional add-on |
| Backups configured but never restored | Test one restore before go-live |
| Handover without documentation | The 4 docs in Stage 5 are deliverables, not extras |
| Third-party ads/links/trackers shipped to production | Sweep every customer-facing surface; only customer-approved external content |
| Warranty covering everything forever | Written terms, fixed period, triage every request |
| Final invoice before written go-live confirmation | Confirmation first, invoice second |
| Calibration data never logged | Stage 8 closes the loop that makes the next quote better |
