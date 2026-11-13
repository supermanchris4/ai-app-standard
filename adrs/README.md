# Architecture decision records

The decisions behind the [AI application standard](../README.md), one architecture decision record (ADR) per decision. The resulting design is in the [design](../docs/design.md). Settings that vary by owner or account are in [configuration-defaults.md](../docs/configuration-defaults.md) and cited by ID (for example [C-02](../docs/configuration-defaults.md#c-02)).

Each ADR is written from the [ADR template](0000-adr-template.md). ADR numbers are never reused.

## Priority

Priority uses MoSCoW. **Must** blocks the template. **Should** is needed before the first real application is built from it. **Could** can follow.

## Statuses

Each ADR and each sub-decision has a status and, separately, the date it took that status.

| Status | Meaning |
|---|---|
| Draft | Still being written |
| Proposed | Ready for its decider to review. Nothing is built on it yet. |
| Interim | The position the work proceeds on until its decider confirms or changes it. If the decider changes it, the ADR is revised in place and its date becomes the day the changed position was adopted. |
| Decided | Confirmed by its decider. It is not edited except to mark it deprecated or superseded, and a changed decision gets a new ADR. |
| Rejected | Declined by its decider while it was Proposed or Interim, so it is not a valid position. It is kept as the record of what was considered. |
| Deprecated | A decided ADR that no longer applies, with nothing replacing it |
| Superseded | A decided ADR whose decision has changed. It names the ADR that replaces it, and the new ADR names it with "Supersedes". |

A decision depends on its sub-decisions, so it is Decided only once each of them is Decided, Rejected or Deprecated.

## Decisions

| ADR | Decision | Priority | Outcome | Status | Date |
|---|---|---|---|---|---|
| [ADR-0001](0001-shape-of-the-outcomes.md) | Shape of the outcomes | Must | Three repositories: the standard, the template, and the platform code released as versioned packages | Decided | 2026-11-12 |
| [ADR-0002](0002-backend-language-and-framework.md) | Backend language and framework | Must | Python and FastAPI, with Ruff and Pyright | Decided | 2026-10-29 |
| [ADR-0003](0003-frontend-framework-and-hosting.md) | Frontend framework and hosting | Must | Vite React single-page app on S3 and CloudFront, with the API as backend-for-frontend | Decided | 2026-10-29 |
| [ADR-0004](0004-identity-and-authorisation-model.md) | Identity and authorisation model | Must | Provider-neutral OIDC with Entra ID as the default, app roles, no role hierarchy, local mock identity provider; the time within which withdrawn access ends is still proposed | Interim | 2026-11-13 |
| [ADR-0005](0005-network-placement.md) | Network placement | Must | Network supplied as inputs, with an optional minimal network from the bootstrap | Decided | 2026-10-29 |
| [ADR-0006](0006-ingress-and-exposure-model.md) | Ingress and exposure model | Must | CloudFront with AWS WAF and an internal load balancer as a VPC origin | Decided | 2026-10-29 |
| [ADR-0007](0007-deployment-pipeline-and-environment-promotion.md) | Deployment pipeline and environment promotion | Must | One build deployed to every environment by image digest, with approvals on test and production | Decided | 2026-10-29 |
| [ADR-0008](0008-repository-home.md) | Repository home | Must | Private repositories in a personal GitHub account | Decided | 2026-10-29 |
| [ADR-0009](0009-aws-bootstrap-resources.md) | AWS bootstrap resources | Should | A bootstrap script per account, with a permissions boundary and S3 native state locking | Decided | 2026-10-29 |
| [ADR-0010](0010-directory-lookups.md) | Directory lookups | Should | Through the API, only where an application needs them | Decided | 2026-10-29 |
| [ADR-0011](0011-local-development-environment.md) | Local development environment | Should | WSL with Docker Engine | Decided | 2026-10-29 |
| [ADR-0012](0012-background-processing-pattern.md) | Background processing pattern | Should | EventBridge Scheduler starting ECS tasks; an SQS worker when needed | Decided | 2026-10-29 |
| [ADR-0013](0013-secrets-management-and-rotation.md) | Secrets management and rotation | Should | Secrets Manager; database credentials fetched with a short cache; RDS-managed rotation | Decided | 2026-10-29 |
| [ADR-0014](0014-developer-access-to-aws.md) | Developer access to AWS | Should | IAM Identity Center single sign-on, with no access keys on laptops | Decided | 2026-10-29 |
| [ADR-0015](0015-github-secrets-variables-and-leak-prevention.md) | GitHub secrets, variables and leak prevention | Should | Identifiers as repository variables named per environment, environment-scoped secrets only where unavoidable, secret scanning in CI and before each commit | Decided | 2026-10-29 |
| [ADR-0016](0016-observability.md) | Observability | Could | CloudWatch with Container Insights and AWS's recommended alarms | Decided | 2026-10-29 |
| [ADR-0017](0017-reporting-and-data-supply.md) | Reporting and data supply | Should | Operational screens in the application, defined exports for data supply, no generic data export | Decided | 2026-10-29 |
| [ADR-0018](0018-api-consumers-and-path-naming.md) | API consumers and path naming | Must | Every interface is an API client; paths name the resource | Decided | 2026-10-29 |
| [ADR-0019](0019-api-exposure.md) | API exposure | Should | Through each application's CloudFront and AWS WAF, built so a gateway can be added later | Decided | 2026-10-29 |
| [ADR-0020](0020-reporting-tool-access-to-application-data.md) | Reporting tool access to application data | Should | Recommended: a shared gateway, reporting views, report audiences entitled to all the data they show, a read-only reporting role and export turned off in reports | Proposed | 2026-11-13 |
