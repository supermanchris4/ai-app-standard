# ADR-0007: Deployment pipeline and environment promotion

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Must

## Question

How do changes move from merge to production, who approves each step, and does Terraform or the pipeline own the running image version?

## Why it matters

This is where the development process binds to the tooling.

## Sub-decisions

| # | Sub-decision | Options | Position | Status | Date |
|---|---|---|---|---|---|
| a | Trigger | Manual dispatch; deploy `dev` on merge to `main` | Deploy `dev` on merge; test and production on approval | Decided | 2026-10-29 |
| b | Promotion | Rebuild per environment; build once and deploy the same image digest to each environment | Build once and deploy the same build everywhere, with only configuration differing | Decided | 2026-10-29 |
| c | Approvals | None; GitHub Environment required reviewers on test and production | Required reviewers and restricted deployment branches on each GitHub Environment ([C-14](../docs/configuration-defaults.md#c-14)) | Decided | 2026-10-29 |
| d | Image version owner | A Terraform variable; the pipeline registers task definition revisions and Terraform ignores them | The pipeline, with immutable SHA tags | Decided | 2026-10-29 |
| e | Infrastructure changes | Plan on PR, then apply on manual dispatch; plan on PR, apply only from `main` through an approved environment | The latter, with a read-only role for plans on pull requests | Decided | 2026-10-29 |
| f | Database schema migrations | Manual; at API start-up; a one-off ECS task run by the pipeline before the service update | Pipeline-run one-off task | Decided | 2026-10-29 |
| g | Third-party actions | Version tags; commit SHAs | Pinned to commit SHAs, with Dependabot proposing updates | Decided | 2026-10-29 |
| h | Workflow token permissions | Set per workflow; none by default, granted per job | None by default, granted per job | Decided | 2026-10-29 |

## Evidence

Practitioner guidance on [OIDC and least-privilege tokens](https://dev.to/indra_gustiprasetya_a80a/github-actions-oidc-to-aws-10-tips-to-kill-static-keys-1fne) and [pinning actions](https://inventivehq.com/blog/github-actions-security-guide). Some environment protection rules on private repositories depend on the GitHub plan ([A-02](../docs/configuration-defaults.md#a-02)).

## Decision

As in the sub-decisions.
