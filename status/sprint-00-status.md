# Sprint 00 status — 2026-11-21

**Goal:** Foundations: confirmed data sources, ADRs and OpenAPI v0, AWS account connected securely, repo + CI deploying "hello" to `dev`, oracle cases for TCO model v0.1.

| Done | In progress | Blocked (why) |
|---|---|---|
| Sprint 0 planned; stories S0-1 to S0-6 written (`docs/stories/`) | S0-1 data sources (Architect), starting | S0-6 AWS connect: waiting for ADR-004 and product owner access (D-01, D-04, D-05) |
| | S0-2 ADR-004 first (Architect), starting; needs D-02, D-03 | S0-3 Part B deploy step: waiting for S0-6 |
| | S0-4 oracle hand-working (Tester), starting | S0-5: waiting for S0-1, S0-2, S0-4, D-08, D-09 |
| | | S0-3 Part A: waiting for ADR-001 and ADR-004 |

**Next:** Architect finishes ADR-004 → Developer starts S0-3 Part A → S0-6 once access arrives → S0-3 Part B.

**Decisions needed from product owner:** see `status/decisions-needed.md`. Most urgent: D-02 region (`eu-north-1` vs. `eu-central-1`) and D-03 one account vs. separate dev/prod accounts, then D-01 account ID + admin login, D-04 GitHub repo, D-05 budget email.

**Risks:** data-source licences and Danish coverage (high); Danish tax rates all `TODO-VERIFY` (high); AWS access lead time blocks the critical path (medium); region choice is hard to reverse (medium).

**Demo in dev:** nothing yet. `dev` URL will be added when S0-3 Part B is done.
