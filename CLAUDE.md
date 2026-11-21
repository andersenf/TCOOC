# CLAUDE.md — Shared rules for all agents

## Product in one line
TCO Cars lets a user pick two cars and compare the total cost of buying, driving and owning them in Denmark, with a focus on electric vs. diesel/petrol.

## Non-negotiables
1. **Source of truth.** `docs/01-product-brief.md` (scope), `docs/02-tco-model-dk.md` (calculations), `docs/adr/` (technical decisions). Don't contradict them; propose a change instead.
2. **No invented data.** Every tax rate, price or car spec in code or config must have a source URL and a "valid from" date. Unknown → mark `TODO-VERIFY` and raise it to the PM.
3. **Config over code.** Danish tax rates, energy prices and defaults live in versioned config (`src/backend/config/dk/*.json`), never hard-coded.
4. **Country-ready.** Danish logic lives behind a `country` module (`dk`). Don't hard-code DK assumptions into shared code. Other countries come later.
5. **Small increments.** One user story per branch/PR. A PR must pass lint, unit tests and the story's acceptance criteria.
6. **Handoffs are written.** Agents communicate through files: stories, ADRs, test reports and `status/`. No hidden state.
7. **Escalate, don't guess.** Ambiguity in scope → PM. Ambiguity in design → Architect. Unresolved → product owner.

## Default tech stack (the Architect may change it through an ADR)
- Frontend: static SPA (TypeScript + Vite, lightweight framework) on S3 + CloudFront
- API: API Gateway HTTP API → AWS Lambda (TypeScript, Node.js LTS)
- Data: DynamoDB (car catalogue cache, reference data); S3 for raw ingested datasets
- Ingestion: scheduled Lambda (EventBridge) pulling free/open data sources
- IaC: AWS CDK (TypeScript); region `eu-north-1` or `eu-central-1` (EU data residency)
- CI/CD: GitHub Actions → `dev` and `prod` stages
- Tests: Vitest (unit), API contract tests, Playwright (E2E)

## Coding conventions
- TypeScript strict mode, ESLint + Prettier, no `any` without a comment.
- Money in DKK as integer øre internally; format only at the UI edge.
- All calculation functions are pure and unit-tested against `tests/oracle/` cases.
- UI language: Danish first, English second (i18n from day one).
- No personal data is stored. No login in v1.

## Definition of Done (every story)
- [ ] Acceptance criteria met and checked by the Tester
- [ ] Unit tests pass; calculation changes are covered by oracle cases
- [ ] IaC updated; deploys cleanly to `dev`
- [ ] Docs/ADRs updated if behaviour or design changed
- [ ] PM marks the story done in `status/`
