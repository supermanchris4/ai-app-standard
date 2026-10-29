# E03 Platform code

Part of the [work plan](../README.md). An enabler Epic: it builds the code every application built from the template needs, first inside identity-spine, then as shared, versioned packages.

## Pitch

Every application built from the template needs sign-in, an API platform with settings, logging, errors, health, database access and audit, a web shell, a local identity provider, Terraform modules and pipelines. The template specifies all of these but holds no code. This Epic builds them once, in identity-spine, in modules kept separate from its domain code ([D-01](../../docs/decision-register.md#d-01-shape-of-the-outcomes)). When a second application needs them, they move to their own repository, tested against the template specifications and released by version tag.

## Benefits

- The security-critical platform code is written, reviewed and tested once.
- A fix reaches every application that upgrades.
- The template stays a set of specifications.

## Measurable results

- Template acceptance criteria met by the platform code's own tests.
- Applications consuming the platform code by version tag, once it is extracted.

## Acceptance criteria

1. The platform code meets each template specification it implements, shown by tests.
2. Once extracted, the platform repository has its own pull request checks, and each release carries a version tag.
3. identity-spine consumes the extracted packages by version tag.

## Dependencies

- identity-spine's rearchitecture, for F03.1.
- [Decision 1](../README.md#decisions-needed), for F03.2.

## Features

| Feature | Type | Horizon | Detail |
|---|---|---|---|
| F03.1 Platform code in identity-spine | Enabler | Now | [f03-1-platform-code-in-identity-spine.md](f03-1-platform-code-in-identity-spine.md) |
| F03.2 Platform repository | Enabler | Later | The platform code moved into its own repository, one folder per package, with pull request checks and a release workflow. The API platform is released as a git tag (installed by uv from a tag and subdirectory), the web platform as an npm package in GitHub Packages, and the mock identity provider as an image in GitHub Container Registry. identity-spine then consumes them by tag. |
| F03.3 Infrastructure modules and workflows | Enabler | Later | The Terraform for each concern in the template's `12-aws-deployment.md`, and the pull request, infrastructure and deployment workflows in `13-delivery-pipeline.md`, as reusable modules and workflows. Claude validates them with `fmt` and `validate`; they are first applied through a pipeline. |
| F03.4 Directory lookup | Enabler | Later | The optional directory lookup endpoint in the API platform, as the template's `20-api-scaffold.md` specifies ([D-10](../../docs/decision-register.md#d-10-directory-lookups)) |
