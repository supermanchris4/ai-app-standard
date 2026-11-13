# ADR-0009: AWS bootstrap resources

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Should

## Question

Should each repository create its own GitHub OIDC provider, Terraform deploy role and state bucket with a bootstrap script, or should they be provided per account?

## Why it matters

An AWS account can hold only one GitHub OIDC provider, so accounts shared between applications need it created once. A role that can create infrastructure needs a permissions boundary. Terraform has deprecated the DynamoDB lock table in favour of native locking in the S3 state bucket.

## Evidence

[HashiCorp: S3 backend](https://developer.hashicorp.com/terraform/language/backend/s3); [HashiCorp community: DynamoDB deprecation](https://discuss.hashicorp.com/t/deprecation-of-dynamodb-table-in-terraform-s3-backend/77060).

## Decision

A bootstrap script per account that creates the OIDC provider (skipped if the account already has one), the Terraform deploy role with a permissions boundary, a read-only plan role and the state bucket with S3 native state locking (`use_lockfile`). It can also create the minimal network in [ADR-0005](0005-network-placement.md). An operator runs it with their own single sign-on credentials rather than credentials passed into a workflow.
