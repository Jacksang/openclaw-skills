---
name: project-delivery
description: "Go-live and handover workflow: local Docker delivery first (cost-saving demo and acceptance), then optional customer-cloud deployment after approval. Includes security verification, handover docs, training, warranty, final payment trigger, and project close. Use when UAT is signed off and the project needs delivery, handover, local deployment, or post-UAT cloud go-live."
allowed-tools: read,write,edit,exec
user-invocable: true
---

# Project Delivery — Local First, Cloud Optional

Deliver a **local Docker environment** the customer can run and approve cheaply. **Cloud production** (e.g. AWS) happens only after written customer approval to create/provision their cloud account — never by default.

## Position in Skill Chain

```
project-planner → project-quoter → ⏸️ SIGN → project-implementation → project-uat → project-delivery
```

## Entry Gate

- [ ] UAT sign-off recorded (`plan/UAT_SIGNOFF.md`)
- [ ] All UAT bugs resolved; build green; all commits pushed
- [ ] `plan/ARCHITECTURE.md` defines Docker Compose local stack + optional cloud target
- [ ] Signed proposal at hand — tier defines training, support, whether cloud go-live is in scope

---

# Part A: Local Delivery (Default — Do This First)

## A1: Local Delivery Package

**Goal:** One-command runnable system on Ubuntu/Linux/macOS via Docker — no cloud spend.

- [ ] `docker-compose.yml` (app, database, cache/reverse-proxy as needed per ARCHITECTURE.md)
- [ ] `.env.example` with documented vars; `.env` gitignored
- [ ] `scripts/local-up.sh` / `local-down.sh` (or `make up` / `make down`)
- [ ] Migrations run cleanly on fresh `docker compose up`
- [ ] Seed data for demo (clearly labeled non-production)
- [ ] `docs/LOCAL_SETUP.md` — prerequisites (Docker version), install steps, ports, troubleshooting

**Output:** `plan/LOCAL_DELIVERY_RUNBOOK.md` — repeatable local delivery steps.

## A2: Local Security & Vulnerability Verification

Run against the **local Docker stack** (same checks as production would get):

### Static & Dependency
- [ ] Dependency audit clean of critical/high
- [ ] Secrets scan — no credentials in repo
- [ ] Debug endpoints and default credentials removed
- [ ] Third-party content sweep on build output and docs

### Runtime (local)
- [ ] Authorization matrix tests pass against local API
- [ ] Rate limiting on auth endpoints
- [ ] Session/token behaviour per ARCHITECTURE.md
- [ ] Baseline vulnerability scan against local URL (e.g. OWASP ZAP → `http://localhost:PORT`)

**Output:** `plan/SECURITY_CHECKLIST.md` (mark environment: local). Failures block customer handover.

## A3: Local Smoke Test & Customer Demo

- [ ] `docker compose up` from clean clone succeeds
- [ ] Health check + one E2E flow per role on localhost
- [ ] Customer walks through core flows on local environment (or recorded demo)
- [ ] Record acceptance in `plan/LOCAL_DELIVERY_SIGNOFF.md` — date, signatory, version/tag

This sign-off may trigger the **final payment milestone** if the proposal ties it to local delivery acceptance.

---

# Part B: Cloud Go-Live (Optional — Customer Approval Required)

**Do not start Part B until:**
- [ ] Part A signed off
- [ ] Customer **in writing** approves cloud account creation / provisioning (who pays, which provider, which region)
- [ ] Cloud scope confirmed (included in contract vs. separate change order)

## B1: Cloud Pre-Flight

- Provision in **customer's cloud account** (AWS or per ARCHITECTURE.md)
- Domain + DNS + TLS; production secrets in customer secret manager
- Production migrations; production seed (no demo users)
- CORS, allowed hosts, production flags

**Output:** extend `plan/DEPLOYMENT_RUNBOOK.md` with cloud section.

## B2: Cloud Security Re-Verification

Re-run A2 checklist against staging/production URL. Update `SECURITY_CHECKLIST.md` with cloud pass/fail.

## B3: Cloud Deploy & Smoke Test

- Deploy via CI/CD pipeline
- Production smoke test + rollback rehearsal
- Record `plan/CLOUD_GO_LIVE_SIGNOFF.md`

---

# Part C: Handover, Training, Close (After Part A; Cloud Optional)

## C1: Operability

**Local:** document backup of Docker volumes, log locations, restart procedure.

**Cloud (if Part B done):** monitoring, alerting, automated DB backups with **one tested restore**, rollback in runbook, infra cost vs. proposal estimate.

## C2: Handover Documentation

Produce in `docs/`:

| Document | Audience |
|----------|----------|
| `LOCAL_SETUP.md` | Anyone running locally |
| `ADMIN_GUIDE.md` | Customer admin |
| `USER_GUIDE.md` | End users (reuse UAT scripts) |
| `API_REFERENCE.md` | Future developers |
| `OPERATIONS.md` | Runbook — local first; cloud section if applicable |

## C3: Training (If Sold)

Deliver per signed tier; log in `plan/TRAINING_LOG.md`.

## C4: Warranty & Post-Launch Triage

Define `plan/WARRANTY_TERMS.md`. Triage requests per project-implementation canon (defect vs change request).

## C5: Final Payment & Project Close

- [ ] Written sign-off that matches proposal milestone (local and/or cloud)
- [ ] Log actual **human + AI hours** to `plan/ACTUAL_VS_ESTIMATE.md` → roll up to quoter `calibration-log.md`
- [ ] Project retrospective
- [ ] Archive: release tag, deliverables bundle, credential handover and rotation

---

## Antipatterns

| Antipattern | Fix |
|-------------|-----|
| Provisioning AWS before local acceptance | Part A first — saves cost, faster feedback |
| Cloud account in your name by default | Customer's account after written approval |
| Skipping security scan on local | Run A2 before handover |
| Stale Docker docs | LOCAL_SETUP must match actual compose file |
| Final invoice before sign-off | Sign-off first |
| Third-party ads/trackers in deliverables | Sweep all customer-facing artifacts |
