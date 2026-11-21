# Decisions needed from the product owner

Running list. The PM adds items, the product owner answers inline (or in chat), and the PM moves answered items to "Resolved" with the date.

## Open

| ID | What we need | Why / what it blocks | Needed by | Options / notes |
|---|---|---|---|---|
| D-01 | **AWS account ID(s)** and an **admin login for the one-time bootstrap** | S0-6 cannot start without access. The admin login is used only to bootstrap CDK, create the OIDC provider, deploy role and budget; after that day-to-day work uses SSO/short-lived credentials | Before S0-6 (target: day 3) | Please share the login through a secure channel, never in the repo or a status file. After bootstrap, you can rotate or remove it |
| D-02 | **Region: `eu-north-1` (Stockholm) or `eu-central-1` (Frankfurt)** | ADR-004 and `cdk bootstrap` in S0-6 | Before ADR-004 is final (target: day 2) | The Architect will add a short cost/latency/service-availability comparison in ADR-004. Both keep data in the EU. Changing region later means redeploying everything |
| D-03 | **One AWS account with `dev`/`prod` stages, or separate `dev` and `prod` accounts** | ADR-004 and the scope of S0-6 | Before ADR-004 is final (target: day 2) | One account: simpler and cheaper to run. Separate accounts: stronger isolation of prod, a little more setup. The Architect will recommend one in ADR-004; your call is final because it affects cost and access |
| D-04 | **GitHub repo owner and name** (e.g. `<owner>/<repo>`), and confirmation that the repo exists with GitHub Actions enabled | The OIDC trust policy in S0-6 is locked to this exact repo | Before S0-6 (target: day 3) | Also tell us whether the repo is private or public |
| D-05 | **Email address for the AWS budget alert** | S0-6 budget alert (about 20 USD/month) | Before S0-6 (target: day 3) | You will need to confirm the subscription email AWS sends |
| D-06 | **Who approves deploys to `prod`** | ADR-004 promotion path; GitHub environment protection rule | Before ADR-004 is final | Default proposal: only the product owner, via a GitHub environment approval. No prod deploy is planned in Sprint 0 |
| D-07 | **Confirm the budget threshold and the response to an alert** | S0-6 | Before S0-6 | Proposal: 20 USD/month, alerts at 80% actual and 100% forecast, email only (no automatic shutdown) |
| D-08 | **Approve the data sources** (after S0-1) | Licence implications (approval required by the factory workflow). E1/E2 stories in Sprint 1 depend on it | After S0-1 delivers `docs/data-sources.md` and ADR-002 (target: day 6) | The PM will send a one-page summary: accepted / rejected / fallback per need |
| D-09 | **Approve TCO model v0.1** (`docs/02-tco-model-dk.md`) | The oracle cases (S0-4) and the E3 engine are built against it | Before S0-4 sign-off (target: day 6) | Points worth a conscious yes/no: outstanding loan `B(Y)` shown but not added to TCO; used cars use the new-car curve shifted by `A0` (no mileage adjustment); break-even in whole years only, anchored on the car with the higher `P`; no inflation/discounting; registration tax assumed included in `P` |
| D-10 | **Any ADR that changes cost, region or stack** (ADR-001, ADR-004) | Required approval per `docs/00-factory-workflow.md` | When the Architect publishes them (target: day 2–4) | The PM will flag which parts need your approval |

## Resolved
_None yet._
