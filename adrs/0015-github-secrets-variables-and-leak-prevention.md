# ADR-0015: GitHub secrets, variables and leak prevention

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Should

## Question

Which pipeline values are GitHub variables and which are secrets, at what scope, and how are secrets stopped from reaching GitHub?

## Why it matters

With OIDC, the pipelines hold no AWS credentials. Most remaining values (role ARNs, region, VPC and subnet IDs, the Terraform backend settings) are identifiers rather than secrets. A secret pushed to a repository must be treated as exposed, even if it is deleted later.

## Sub-decisions

| # | Sub-decision | Options | Position | Status | Date |
|---|---|---|---|---|---|
| a | Non-sensitive identifiers | GitHub secrets; GitHub variables | Variables | Decided | 2026-10-29 |
| b | Scope | Repository; GitHub Environment | Repository variables, named for the environment where they differ by stage, such as `TEST_DEPLOY_ROLE_ARN`. GitHub Environments carry the gates and the deploy roles' trust, not variables: a job that calls a reusable workflow cannot read an environment's variables to pass as inputs, and some plans have no environments on a private repository ([A-02](../docs/configuration-defaults.md#a-02)). | Decided | 2026-11-13 |
| c | Sensitive values a pipeline needs | GitHub secrets; read from Secrets Manager at run time through OIDC | Read from Secrets Manager at run time. Where a GitHub secret is unavoidable, it is environment-scoped behind required reviewers. | Decided | 2026-10-29 |
| d | Leak prevention | GitHub push protection; a secret scan in the pull request checks; a pre-commit secret scan on developers' machines | All three where available. Push protection on private repositories depends on the GitHub plan ([A-02](../docs/configuration-defaults.md#a-02)), so the pull request checks run an open-source secret scanner as well. | Decided | 2026-10-29 |

## Evidence

[GitHub: secret scanning and push protection](https://docs.github.com/en/code-security/secret-scanning/working-with-secret-scanning-and-push-protection); [GitHub: reusing workflows](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows), on what a calling job can and cannot set; practitioner guidance on [managing secrets in GitHub Actions](https://www.blacksmith.sh/blog/best-practices-for-managing-secrets-in-github-actions).

## Decision

As in the sub-decisions.
