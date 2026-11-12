# E02 Template specifications

Part of the [work plan](../README.md). An enabler Epic: it completes the template's specifications, repository settings and guides, points them at the platform code and proves them with a new application.

## Pitch

An application is created from the template and built from its specifications with an AI coding assistant. This Epic completes what the template still lacks, then points the specifications at the platform code, so an application takes the platform by version and generates only its own code. A new application proves the result by deploying from the template to AWS.

## Benefits

- A new application starts from complete specifications rather than filling gaps itself.
- The specifications say which code comes from the platform and which the application writes.

## Measurable results

- An application is created from the template and deployed end to end without a change to the template that is specific to that application.
- Gaps that application finds are fixed in the template specifications.

## Acceptance criteria

1. The template implements the [design](../../docs/design.md), as specifications only.
2. Each specification that the platform code implements names the package and the version that provides each part.
3. A new application has been created from the template and deployed end to end to AWS.

## Dependencies

- [E03 Platform code](../e03-platform-code/README.md), for F02.5 and F02.6.

## Features

| Feature | Type | Horizon | Detail |
|---|---|---|---|
| F02.1 Specification baseline | Enabler | Done | The generic specifications and AI working method: Claude Code settings and stop rules, the ADR template, generation rules, data classification, the architecture, authentication, deployment and local development specifications, the delivery plan stub, Dependabot and the pre-commit secret scan |
| F02.2 Repository settings and versions | Enabler | Done | The pull request template, the `CODEOWNERS` stub and the pinned Python, Node and PostgreSQL versions |
| F02.3 Guides | Enabler | Done | The developer setup guide in the standard, and the runbook stub and `TEMPLATE_USAGE.md` in the template |
| F02.4 Bootstrap network | Enabler | Now | [f02-4-bootstrap-network.md](f02-4-bootstrap-network.md) |
| F02.5 Platform code references | Enabler | Now | [f02-5-platform-code-references.md](f02-5-platform-code-references.md) |
| F02.6 Template proof | Enabler | Later | A new application created from the template and deployed end to end to AWS, with fixes made in the template ([decision 2](../README.md#decisions-needed)) |
