# Decisions needed from the product owner

Running list. The PM adds items, the product owner answers inline (or in chat), and the PM moves answered items to "Resolved" with the date.

## Open

| ID | What we need | Why / what it blocks | Needed by | Options / notes |
|---|---|---|---|---|
| D-07 | **Record the budget values you used** | ADR-004 §7 settings table is the versioned record of the manual budget | Before S0-6 sign-off | 2026-11-23: the product owner created the budget with **different values** from the proposal (20 USD/month, 80% actual, 100% forecast, email only). Please send the amount, thresholds and actual/forecast choice |
| D-08 | **Approve the data sources** (after S0-1) | Licence implications (approval required by the factory workflow). E1/E2 stories in Sprint 1 depend on it | After S0-1 delivers `docs/data-sources.md` and ADR-002 (target: day 6) | The PM will send a one-page summary: accepted / rejected / fallback per need |
| D-09 | **Approve TCO model v0.1** (`docs/02-tco-model-dk.md`) | The oracle cases (S0-4) and the E3 engine are built against it | Before S0-4 sign-off (target: day 6) | Points worth a conscious yes/no: outstanding loan `B(Y)` shown but not added to TCO; used cars use the new-car curve shifted by `A0` (no mileage adjustment); break-even in whole years only, anchored on the car with the higher `P`; no inflation/discounting; registration tax assumed included in `P` |
| D-10 | **Any ADR that changes cost, region or stack** (next: ADR-001) | Required approval per `docs/00-factory-workflow.md` | When the Architect publishes them | ADR-004 approved 2026-11-23 (see Resolved). ADR-001 is next |

## Resolved

| ID | Decision | Date | Answer | Follow-up |
|---|---|---|---|---|
| D-02 | Region | 2026-11-21 | **`eu-west-1` (Ireland).** Not one of the two listed options (`eu-north-1`, `eu-central-1`). Keeps data in the EU | `CLAUDE.md` updated (D-11). The Architect is recording it in ADR-004 |
| D-03 | Account and stage layout | 2026-11-21 | **One AWS account covering frontend and backend, with only a `prod` stage (no `dev`).** After the first major version (v1.0.0), new features ship behind feature flags | The Architect is recording it in ADR-004. S0-6 and S0-3 Part B target `prod`. Feature-flag approach to be designed by the Architect before v1.0.0 |
| D-06 | Who approves prod deploys | 2026-11-21 | **The product owner approves prod deploys, but only from v1.0.0 onward.** Until v1.0.0, merging to main deploys to prod automatically with no approval gate (PR checks must pass first), and agents may merge | The Architect is recording it in ADR-004. Repo is public on Free (D-04), so GitHub environment required reviewers can be used for the gate |
| D-11 | Change docs from a `dev` stage to a single `prod` stage | 2026-11-21 | **Approved.** Applies to `CLAUDE.md`, `docs/00-factory-workflow.md`, Sprint 0 stories (S0-2, S0-3, S0-5, S0-6), backlog, parking lot, templates and status files. Also approved: no local deploys (developer login is read-only and can run `cdk diff`; all prod changes through CI from `main`; only the product owner's admin login deploys by hand, once, for the bootstrap); budget alert created manually by the product owner per the ADR-004 runbook, not in CDK | Docs updated 2026-11-21. ADR-004 being updated by the Architect; approval tracked in D-10 |
| D-01 | AWS account and access | 2026-11-23 | **Account created** (regular account on the advanced AWS experience, not a "project"), Region `eu-west-1`. CLI profile `tco-cars` signed in via `aws login`; AWS Agent Toolkit (`aws-mcp` server, AWS skills) installed for Claude Code | Account ID is deliberately **not** recorded here (public repo, ADR-004 §5); it lives only in the local AWS profile and, later, in GitHub Actions secrets |
| D-13 | Agent write access to AWS | 2026-11-23 | **Until v1.0.0, Claude and the agents may read and write AWS, including local `cdk deploy`.** From v1.0.0, agent access becomes read-only and every `prod` change goes through CI with product owner approval. Partly replaces the "no local deploys" part of D-11 | `CLAUDE.md`, ADR-004 and S0-6 updated 2026-11-23; approval tracked in D-10 |
| D-10 (ADR-004) | Approve ADR-004 | 2026-11-23 | **Approved.** ADR-004 status set to Accepted | ADR-001 still needs approval when published |
| D-04 | GitHub repo | 2026-11-23 | **`andersenf/TCOOC`, public, GitHub Free.** Created 2026-11-23 by Claude Code at the product owner's request; `main` pushed | Filled into the ADR-004 OIDC trust policies. Ruleset on `main` is set up in S0-6 once CI checks exist |
| D-05 | Budget alert email | 2026-11-23 | **Budget alert created by the product owner** in the AWS console; email not recorded in the repo | Values tracked in D-07 |
| D-12 | Commit author email privacy | 2026-11-23 | **Repo commits use `noreply@el-efant.online`** (repo-local git config). Both existing commits rewritten before the first push; backup refs pruned, so the personal address is not in the history | Optional: add and verify that address on the GitHub account so commits link to the profile |
