# Sprint 00 status — 2026-11-21

**Goal:** Foundations: confirmed data sources, ADRs and OpenAPI v0, AWS account connected securely, repo + CI deploying "hello" to `prod`, oracle cases for TCO model v0.1.

| Done | In progress | Blocked (why) |
|---|---|---|
| Sprint 0 planned; stories S0-1 to S0-6 written (`docs/stories/`) | S0-1 data sources (Architect), starting | S0-6 AWS connect (`prod`, `eu-west-1`): waiting for approval of revised ADR-004 (D-10) and product owner input (D-01 account + admin login, D-04 repo owner/name, D-05 budget email, D-07 thresholds) |
| Product owner decided D-02 (region `eu-west-1`), D-03 (one account, `prod` stage only), D-06 (prod approval from v1.0.0; auto-deploy on merge until then) | S0-2 ADR-004 being revised by the Architect; then awaits product owner approval (D-10) | S0-3 Part B CI deploy to `prod` on merge: waiting for S0-6 |
| D-11 approved: docs moved from `dev` to a single `prod` stage; no local deploys (read-only developer login, `cdk diff` only); budget created manually by the product owner. Stories S0-2, S0-3, S0-5, S0-6, backlog, parking lot and templates updated | S0-4 oracle hand-working (Tester), starting | S0-5: waiting for S0-1, S0-2, S0-4, D-08, D-09 |
| Repo confirmed public, GitHub Free plan (D-04, owner/name still open) | | S0-3 Part A: waiting for ADR-001 and approved ADR-004 |

**Next:** Architect finishes revised ADR-004 → product owner approves (D-10) → Developer starts S0-3 Part A → product owner fixes commit email (D-12) and pushes → branch protection on `main` → product owner runs the one-time bootstrap and creates the budget (S0-6) → S0-3 Part B (CI deploy to `prod` on merge).

**Decisions needed from product owner:** see `status/decisions-needed.md`. Most urgent: D-01 AWS account ID + admin login (details coming shortly), D-04 repo owner/name, D-05 budget email, D-07 budget thresholds, D-12 commit author email before the first push. Also: D-10 approve revised ADR-004.

**Risks:** data-source licences and Danish coverage (high); Danish tax rates all `TODO-VERIFY` (high); no `dev` stage, so every merge before v1.0.0 goes straight to `prod`, and a broken merge is visible at the public URL until fixed (medium; mitigated by required PR checks, branch protection on `main` and nothing real in prod before v1.0.0); branch protection must be on before the first auto-deploy (medium, D-04); personal email exposed in public commit history if pushed as is (medium, D-12); AWS access lead time blocks the critical path (medium).

**Demo in prod:** nothing yet. `prod` URL will be added when S0-3 Part B is done.
