# Initial backlog

## Sprint 0 — Foundations
- **S0-1 (Architect)** Validate data sources → `docs/data-sources.md`, ADR-002
- **S0-2 (Architect)** ADR-001 stack, ADR-003 calculation engine, ADR-004 environments/CI; OpenAPI v0
- **S0-3 (Developer)** Scaffold the repo, CDK app (frontend/api/data stacks), CI pipeline, deploy "hello" to `prod` on merge to `main`
- **S0-4 (Tester)** Write 9 oracle cases from `02-tco-model-dk.md`; set up the test harness
- **S0-6 (Developer + Architect, product owner provides access)** Connect to the AWS account: one account with a `prod` stage only, region `eu-west-1` (ADR-004); read-only local access via AWS IAM Identity Center (SSO) or a named CLI profile that can run `cdk diff` (no local deploys, no long-lived access keys in the repo); one-time `cdk bootstrap` by the product owner's admin login; a GitHub Actions OIDC identity provider with a least-privilege CI deploy role for `prod` and a read-only role for PR diffs; branch protection on `main`; an AWS Budgets alert (about 20 USD/month) created manually by the product owner. Done when CI deploys "hello" to `prod` from `main`. Depends on the product owner supplying the account ID, an admin login for the one-time bootstrap, the repo owner/name and the budget email
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
