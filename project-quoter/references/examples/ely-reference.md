# Example: ELY Wellness Platform

Canonical reference implementation of the Project Quoter methodology.

## Project Context
- **Client:** ELY Coffee + Thermal Wellness Screening
- **Stack:** AWS (RDS PostgreSQL, S3, API Gateway, Lambda), React, WhatsApp Business API, Thermal camera SDK
- **Commitment:** $45K SGD Contracted MVP + $38K SGD Conditional = $83K SGD Total

## File Map

| File | Purpose | Lines |
|------|---------|-------|
| `plan/Draft scope.md` | Stage 1: Feature catalog with 19 features across 3 phases | 200+ |
| `plan/E01-onboarding-rbac.md` | Multi-channel onboarding with 12 user stories, 6 tasks, 48 unit tests | 200+ |
| `plan/E02-therapy-sessions.md` | Therapy session tracking module | — |
| `plan/E03-imaging-hardware.md` | Thermal camera SDK integration | — |
| `plan/E04-reporting-distribution.md` | PDF generation + WhatsApp/email delivery | — |
| `plan/E05-ecommerce-transactions.md` | Product catalog + order lifecycle | — |
| `plan/E06-referral-rewards.md` | Referral codes + reward tracking | — |
| `plan/E07-infrastructure.md` | AWS provisioning with Terraform IaC | — |
| `plan/E08-loyalty-framework.md` | Points engine + tier management | — |
| `plan/E09-order-fulfillment.md` | Delivery logistics + carrier integration | — |
| `plan/E10-mobile-app.md` | React Native/Flutter native app | — |
| `plan/E11-campaign-engine.md` | Merchant campaign builder | — |
| `plan/E12-ai-image-analysis.md` | SageMaker ML pipeline | — |
| `plan/E13-payment-gateway.md` | Stripe/Adyen PCI-DSS integration | — |
| `plan/E14-analytics-dashboards.md` | Real-time Kinesis + OLAP | — |
| `plan/E15-training-certification.md` | LMS + certificates | — |
| `plan/E16-multimodal-input.md` | Voice + image fusion | — |
| `plan/E17-user-segmentation-recsys.md` | Vector DB + collaborative filtering | — |
| `plan/E18-web3-tokenization.md` | Polygon smart contracts | — |
| `plan/E19-marketplace-expansion.md` | Multi-tenant marketplace | — |
| `plan/MASTER_SUMMARY.md` | Stage 3: Aggregate across 13 old-style modules (M01–M13) | 200+ |
| `plan/PROJECT_PLAN.html` | Stage 4: Client-facing project plan with Gantt | 422 |
| `plan/PROPOSAL.html` | Stage 5: Formal business proposal (signable artifact) | 433 |

Full source: `~/projects/ely/plan/`

## Key Numbers

| Metric | Value |
|--------|-------|
| Total Modules | 19 (E01–E19) |
| Delivery Phases | 3 |
| Phase 1 Dev + Test | 1,064 hrs |
| Phase 2 Dev + Test | 2,422 hrs |
| Contracted Price | $45,000 SGD |
| Conditional Price | $38,000 SGD |
| Total Commitment | $83,000 SGD |
| MVP Timeline | 8 weeks |
| Traditional Equivalent | ~$1,047,000 SGD, ~40 weeks, 5–11 people |

## Evolution of the Methodology

### v1 (M01–M13): Initial modular breakdown
- Used sequential module IDs (M01–M13)
- 13 modules, 83 tasks, 394.5 hours
- Simpler infrastructure assumptions (DynamoDB, Lambda)

### v2 (E01–E19): Expanded scope with new requirements
- Renumbered as E01–E19 for clarity
- 19 modules with more detailed scope
- Upgraded to PostgreSQL + more robust infrastructure
- Added Phase 3 AI/Web3/Marketplace modules

### v2.3 (Final Proposal): Client-ready with formatting polish
- Multiple iterations on HTML formatting
- Fixed total row contrast issues
- Added thermal screening qualification note as callout
- Adjusted cloud cost estimates to realistic buffer ($20–40/month)
