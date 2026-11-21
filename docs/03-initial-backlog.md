# Initial backlog

## Sprint 0 — Foundations
- **S0-1 (Architect)** Validate data sources → `docs/data-sources.md`, ADR-002
- **S0-2 (Architect)** ADR-001 stack, ADR-003 calculation engine, ADR-004 environments/CI; OpenAPI v0
- **S0-3 (Developer)** Scaffold the repo, CDK app (frontend/api/data stacks), CI pipeline, deploy "hello" to `dev`
- **S0-4 (Tester)** Write 9 oracle cases from `02-tco-model-dk.md`; set up the test harness
- **S0-6 (Developer + Architect, product owner provides access)** Connect to the AWS account: decide the account setup (single account with `dev`/`prod` stages vs. separate accounts) in ADR-004; set up local access via AWS IAM Identity Center (SSO) or a named CLI profile (no long-lived access keys in the repo); `cdk bootstrap` the chosen EU region; create a GitHub Actions OIDC identity provider and a least-privilege deploy role; set an AWS Budgets alert (about 20 USD/month). Done when `cdk deploy` to `dev` works both locally and from CI. Depends on the product owner supplying the account ID, region choice and an admin login for the one-time bootstrap
- **S0-5 (PM)** Turn epics E1–E5 into stories; plan Sprint 1

## Epics
- **E1 Car catalogue** — ingest car data, search/select a car, show specs
- **E2 Danish reference data** — tax rules, electricity and fuel prices in config, plus a `GET /reference/dk` endpoint
- **E3 TCO engine** — pure calculation module, `POST /tco/compare`, passes the oracle
- **E4 Comparison UI** — two pickers, assumptions form, results table, cumulative-cost chart, break-even, DA/EN
- **E5 Trust & sharing** — source tooltips, assumptions panel, shareable URL, basic analytics without personal data

## First user stories (examples)
- *As a buyer, I can search for a car by make and model so I can pick the exact variant.* (E1)
- *As a buyer, I see the total cost over my ownership period for both cars side by side.* (E3/E4)
- *As a buyer, I can change km/year and see the result update in under 1 second.* (E4)
- *As a buyer, I can see where every number comes from.* (E5)
