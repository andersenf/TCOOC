# Sprint 00 status — 2026-11-23

**Goal:** Foundations: confirmed data sources, ADRs and OpenAPI v0, AWS account connected securely, repo + CI deploying "hello" to `prod`, oracle cases for the TCO model.

| Done | In progress | Not started / blocked (why) |
|---|---|---|
| Sprint 0 planned; stories S0-1 to S0-6 written (`docs/stories/`) | TCO model v0.2 being written by the Architect (product owner review, 2026-11-23: no break-even, cash only, period 1–10 years default 4, odometer input, value 0 at 400,000 km petrol/diesel and 250,000 km EV/PHEV). Then awaits D-09 | S0-1 data sources (Architect): not started; next after the model |
| **ADR-004 accepted** (D-10, 2026-11-23): one account, `prod` only, `eu-west-1`, GitHub OIDC, manual budget, approval gate from v1.0.0. Merged in PR #1 | | S0-2 rest (ADR-001, ADR-003, OpenAPI v0): not started; ADR-001 next |
| Repo `andersenf/TCOOC` created (public, Free); commit email `noreply@el-efant.online`, old commits rewritten before the first push; GitHub email set to private | | S0-4 oracle cases (Tester): not started; waits for TCO model v0.2 |
| AWS account in `eu-west-1`, CLI profile `tco-cars`, AWS Agent Toolkit installed (D-01, D-13: agents may write to AWS until v1.0.0) | | S0-3 Part A (scaffold + CI checks): waits for ADR-001 |
| Budget alert created by the product owner: 100 USD/month, alerts at 60% and 80% (D-05, D-07) | | S0-6 (bootstrap, OIDC roles, `main` ruleset): waits for S0-3 Part A |
| | | S0-3 Part B (CI deploy to `prod`): waits for S0-6. S0-5: waits for S0-1, S0-2, S0-4, D-08, D-09 |

**Next:** TCO model v0.2 → product owner approves (D-09) → Architect: ADR-001, then S0-1 and ADR-003/OpenAPI v0; Tester: S0-4 oracle cases → Developer: S0-3 Part A → S0-6 → S0-3 Part B.

**Decisions needed from product owner:** see `status/decisions-needed.md`. Open: D-09 (TCO model v0.2), D-08 (data sources, after S0-1), D-10 for ADR-001 when published.

**Temporary AWS resources (delete when S0-3 is done):**
- S3 bucket `tcooc-hello-test-cf573f9d` (`eu-west-1`): public "Hej verden" test page via S3 static website hosting, HTTP only, created 2026-11-23 at the product owner's request. Tagged `purpose=s0-hello-test`, `delete-after=S0-3`. Delete with `aws s3 rb s3://tcooc-hello-test-cf573f9d --force --profile tco-cars --region eu-west-1`.

**Risks:** data-source licences and Danish coverage (high); Danish tax rates all `TODO-VERIFY` (high); no `dev` stage, so every merge before v1.0.0 goes straight to `prod` (medium; mitigated by PR checks and the `main` ruleset from S0-6); until the ruleset exists, direct pushes to `main` are possible (medium, closed in S0-6); agents have admin write access to AWS until v1.0.0 (D-13; mitigated by CDK-first and the guarded local-deploy wrapper); one merge commit (`8740b6e`) shows the product owner's personal email, accepted by the product owner on 2026-11-23.

**Demo in prod:** nothing from CI yet. Temporary S3 test page: http://tcooc-hello-test-cf573f9d.s3-website-eu-west-1.amazonaws.com
