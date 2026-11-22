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
- IaC: AWS CDK (TypeScript); region `eu-west-1` (Ireland, EU data residency), see ADR-004
- CI/CD: GitHub Actions → `prod` (single stage; new features behind feature flags after v1.0.0), see ADR-004
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
- [ ] IaC updated; deploys cleanly to `prod` and the post-deploy smoke test passes
- [ ] Docs/ADRs updated if behaviour or design changed
- [ ] PM marks the story done in `status/`

## AWS access for agents
- Until v1.0.0, agents may read and write AWS (CLI profile `tco-cars`, `aws-mcp` server), including local `cdk deploy`, as decided by the product owner on 2026-11-23 (D-13). Prefer CDK over console or CLI changes so the IaC stays the source of truth; CI from `main` remains the normal deploy path. From v1.0.0, agent access becomes read-only and every `prod` change goes through CI with product owner approval (ADR-004).
- Never write account IDs or ARNs into the repo (it is public).

<!-- BEGIN AWS Agent Toolkit rules -->
# AWS Guidance

- Where these AWS rules conflict with the project's own instructions, the
  project's instructions take precedence.
- Prefer the AWS MCP Server for AWS interactions — it provides sandboxed
  execution, observability, and audit logging. If unavailable, use the
  AWS CLI directly.
- For any AWS task or question — building, configuring, migrating, debugging,
  or operating a service, writing SDK code, authoring CDK/CloudFormation, or
  answering how a service works — first call `aws___search_documentation` with
  topic `agent_skills` and load the matching skill with `aws___retrieve_skill`.
  Do this even when you believe you know the answer: skills carry current
  service behavior that your training may not. Prefer the skill's guidance over
  general knowledge. Skip it only for work that does not involve AWS.
- When uncertain about specific AWS details (API parameters, permissions,
  limits, error codes), verify against documentation rather than guessing.
  State uncertainty explicitly if you cannot confirm.
- Before reporting a count, a total, or that something does not exist, read
  every page of the results and search every Region and scope where it could
  be. If you could not, say the result is partial and name what you checked.
- To say what a system currently does, read what the runtime uses: the
  completed deployment, the alias or version that is invoked, the template the
  stack is running. A newer stored version, a draft, or a description field is
  not evidence of live behavior.
- Present a cause as confirmed only if data, configuration, or logs you read
  show it. Label anything else as unverified, and say what you would check
  next.
- Before you interpret the records that show a failure, such as error log
  lines, failed executions, or table items, read one of them whole, with
  every field. Don't truncate messages or select fields in your own code
  until you have.
- When an error says a value was undefined, null, empty, or missing, find
  the input, query result, or item that supplied that value. If the account
  shows why it was empty, such as no item matching the key, read that
  before you call the cause unverified.
- When a field in a record you read explains the result, such as an error
  counter, an empty array, a null, or a state value, name the field and its
  exact value in the answer, and say which record it came from.
- When the user asks a specific question or names a suspected cause, confirm
  or rule out each cause they raised, and do not offer alternatives your evidence
  does not support.
- When creating infrastructure, prefer infrastructure-as-code (AWS CDK or
  CloudFormation) over direct CLI commands.
- When working with infrastructure, follow AWS Well-Architected Framework
  principles.
- Do not use em dashes in AWS resource names or descriptions. Use
  hyphens instead.

## Before you conclude

- Before deciding the AWS account can't hold what the request asks for,
  look. List the places it could live, such as S3 buckets, databases, and
  log groups, and read what is there.
- Don't conclude that records are missing from metadata, such as a
  creation date or a stored-bytes count, or from one query that failed or
  came back empty. Read the records another way, across the full time
  range, before reporting them absent.
- A tool's own error is evidence. If a call the answer depends on fails
  with expired or missing credentials or AccessDenied, report that error
  and what it blocks, and don't overrule it with another check. Searching
  other Regions is for finding resources, not for retrying an auth error.
  Retry throttling with backoff before you report it.

## Secret Safety

- MUST load the `aws-secrets-manager` skill first for any secret,
  credential, API key, token, or password task. MUST NOT call
  `secretsmanager get-secret-value` or `batch-get-secret-value`, and MUST
  NOT hit the Secrets Manager Agent daemon directly. MUST use
  `{{resolve:secretsmanager:secret-id:SecretString:json-key}}` with
  `asm-exec` so the secret resolves at runtime without entering context.
<!-- END AWS Agent Toolkit rules -->
