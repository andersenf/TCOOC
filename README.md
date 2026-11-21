# TCO Cars — AI Software Factory

This repo sets up a team of four AI agents that build **TCO Cars**. TCO Cars is a web app on AWS that compares the total cost of ownership of two cars, focused on electric vs. diesel/petrol, for Danish owners first.

## The team

| Agent | File | Owns |
|---|---|---|
| Project Manager | `.claude/agents/project-manager.md` | Backlog, sprint plan, status, handoffs, acceptance |
| Architect | `.claude/agents/architect.md` | Architecture, ADRs, API contracts, data model, data-source validation, IaC design |
| Developer | `.claude/agents/developer.md` | Code (frontend, Lambdas, IaC), unit tests, PRs |
| Tester | `.claude/agents/tester.md` | Test plans, integration/E2E tests, calculation verification, bug reports |

The human (product owner) approves scope, architecture decisions and releases.

## Repository layout

```
CLAUDE.md                     Shared rules every agent follows
.claude/agents/               Agent definitions (Claude Code subagents)
docs/
  00-factory-workflow.md      How work flows between agents
  01-product-brief.md         What we are building and why
  02-tco-model-dk.md          Danish cost model: parameters & formulas
  03-initial-backlog.md       Epics and first user stories
  adr/                        Architecture Decision Records
  templates/                  User story, ADR, test plan, bug, status report
status/                       PM writes sprint status here
src/                          (created by Developer) frontend/, backend/, infra/
tests/                        (created by Developer/Tester)
```

## How to start

1. Create a git repo from these files and open it in Claude Code.
2. Product owner reviews `docs/01-product-brief.md` and `docs/02-tco-model-dk.md`.
3. Kick off: *"Use the project-manager agent to plan Sprint 0 from docs/03-initial-backlog.md."*
4. Sprint 0 = Architect validates the data sources and writes ADR-001…004. Developer scaffolds the AWS stack. Tester writes the calculation test oracle.
5. From then on, the PM runs each sprint using the loop in `docs/00-factory-workflow.md`.
