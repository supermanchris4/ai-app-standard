# ADR-0005: Network placement

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Must

## Question

Should the application's Terraform create its own VPC, or deploy into a network supplied to it?

## Why it matters

Where an account already has a network, an application that creates its own duplicates it and may not be permitted. Where an account has none, something must create one. The network is also the largest fixed cost: a NAT gateway, an internal load balancer and a small RDS instance together cost roughly USD 70 or more a month before any Fargate tasks.

## Options

| Option | Description | Assessment |
|---|---|---|
| A. Own VPC per application | Two Availability Zones, a NAT gateway, three subnet tiers | Self-contained. Each application pays for its own NAT gateway. |
| B. Supplied network | VPC ID and two subnet lists (`app_subnet_ids` for tasks and the database, `lb_subnet_ids` for the internal load balancer) passed in as required inputs | Works with any existing network. Fails the plan if a value is missing. |
| C. B, with an optional minimal network from the bootstrap | As B. For an account with no network, the bootstrap creates one VPC with private subnets in two Availability Zones and one NAT gateway, shared by the applications in that account. | Works in both kinds of account, and the application Terraform stays the same |

## Decision

C.
