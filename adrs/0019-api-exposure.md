# ADR-0019: API exposure

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Should

## Question

How do systems outside the application's AWS account reach its API, and should an API management layer sit in front of it?

## Why it matters

Without a common approach, each application exposes its API differently, and adding a gateway later means reworking each one.

## Options

| Option | Description | Assessment |
|---|---|---|
| A. Through each application's CloudFront and AWS WAF | Consumers call the API on the same address the web interface uses, with their own tokens | No new platform, and works from any host. Each application manages its own consumers and documentation. |
| B. An API gateway in front of application APIs | A gateway product publishes application APIs as products | One catalogue, and consistent onboarding, quotas and usage reporting. Needs a platform and an owner. |
| C. Private connectivity between a consumer's network and AWS | A network link for consumer calls | No internet path, but ties the application to where each consumer is hosted |

## Decision

A, built so a gateway can be placed in front later without changing the application: the API validates every token itself rather than relying on a gateway, its contract is published as an OpenAPI document, and each consumer has its own app registration.
