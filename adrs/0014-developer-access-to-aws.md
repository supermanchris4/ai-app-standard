# ADR-0014: Developer access to AWS

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Should

## Question

How do developers get AWS access from their machines, including from inside WSL, and what credentials do they hold locally?

## Why it matters

Long-lived access keys on laptops are a common source of credential leaks. AWS recommends temporary credentials, such as those issued through IAM Identity Center. Local development needs no real application secret, so AWS access is the only credential a developer holds.

## Options

| Option | Description | Assessment |
|---|---|---|
| A. IAM Identity Center single sign-on | Developers sign in with `aws sso login` inside WSL and receive short-lived credentials, cached and refreshed by the AWS CLI | No stored keys. Browser sign-in from WSL may need the device-code option. |
| B. IAM user access keys held in a vault tool | Keys are kept in the operating system's secure store and exchanged for short-lived sessions | Long-lived keys still exist and must be rotated |
| C. No developer access to AWS | All AWS changes go through the pipelines | Simplest to control, but makes troubleshooting slow |

## Evidence

[AWS: programmatic access with temporary credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/security-creds-programmatic-access.html); practitioner guidance on [AWS CLI single sign-on profiles](https://oneuptime.com/blog/post/2026-02-12-aws-cli-sso-profiles/markdown).

## Decision

A, with developer access limited to development accounts or permission sets. Test and production change only through the pipelines.
