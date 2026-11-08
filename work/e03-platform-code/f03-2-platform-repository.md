# F03.2 Platform repository

Part of [E03 Platform code](README.md) in the [work plan](../README.md). An enabler Feature: identity-spine's platform modules move into their own repository, [`ai-app-platform`](https://github.com/supermanchris4/ai-app-platform), as versioned packages, and identity-spine consumes them by tag (its F01.11). Started now by [decision 1](../README.md#decisions-needed).

## Benefits

- The security-critical platform code is reviewed, tested and released in one place, and a fix reaches every application that upgrades.
- A second application starts from released packages instead of copying identity-spine's code.

## Acceptance criteria

1. The repository holds one folder per package: the API platform (`packages/api`), the web platform (`packages/web`) and the mock identity provider (`packages/mock-idp`). No package imports any application's code.
2. Each package's tests run in the repository's pull request checks, which follow the template's `13-delivery-pipeline.md`, and show the template specification each package implements is met.
3. Each package is released by a version tag: the API platform as a git tag installed by uv from the tag and its subdirectory, the web platform as an npm package in GitHub Packages, and the mock identity provider as an image in GitHub Container Registry.
4. A release can be installed by an application and its pipelines without a stored credential ([decision 4](../README.md#decisions-needed)), and the repository's README says how.

## Dependencies

- identity-spine F01.12, which settled which code is platform code.
- [Decisions 3 and 4](../README.md#decisions-needed), decided 2026-11-08: applications own their migrations, and the shared repositories are public under the Apache License 2.0.

## Stories

### S03.2.1 Repository foundation

As the platform maintainer, I want the repository set up like identity-spine, so that its packages are checked and released the same way.

**Acceptance criteria:**

- `README.md`, `CLAUDE.md`, `.gitignore`, `.editorconfig`, `CODEOWNERS`, a pull request template, pre-commit with gitleaks, and Dependabot with the 7-day cooldown, for each package's ecosystem and the actions.
- A `ci.yml` running each package's checks and the security checks (gitleaks, Semgrep, OSV-Scanner), hardened as identity-spine F01.9: actions pinned to commit SHAs, scanners to image digests, `permissions: {}`, and read-only jobs.
- `.github/rulesets/main.json`, not enforced on a private repository under the free plan (identity-spine decision 7).

### S03.2.2 API platform package

As an application developer, I want the API platform as one Python package, so that my application imports it rather than copying it.

**Acceptance criteria:**

- `packages/api` is a uv project, `ai-app-platform`, whose code is under one namespace, `ai_app_platform`: settings (the platform base the application's settings extend), logging, database access, middleware, health, sign-in and access, audit, and the runtime grants for migrations.
- The package owns the SQLAlchemy declarative `Base`, and the application declares its models on it, so the application's Alembic sees the platform's tables. Database access builds no engine when imported: the application creates its engine from its settings.
- The platform's tables (`auth_sessions`, `audit_records`) are created by each application's own migrations ([decision 3](../README.md#decisions-needed)); the package documents the migration a new application starts with.
- The platform's tests move here, with what they need: a PostgreSQL service container and the test token issuer, which is released as a test helper (`ai_app_platform.testing`) the application's tests import.

Work found in identity-spine: [extraction work](README.md#extraction-work-found-in-identity-spine).

### S03.2.3 Web platform package

As an application developer, I want the web platform as an npm package, so that my web interface imports it.

**Acceptance criteria:** `packages/web` is built as a library from identity-spine's `apps/web/src/platform/`, with `react` and `react-router` as peer dependencies and its own type declarations, tested with Vitest, and published as `@supermanchris4/ai-app-platform-web` in GitHub Packages. The component kit is not part of it (identity-spine decision 9).

### S03.2.4 Mock identity provider image

As an application developer, I want the mock identity provider as a released image, so that my local stack pulls it instead of building it.

**Acceptance criteria:** `packages/mock-idp` is identity-spine's `infra/local/mock-idp/`, which already reads each application's roles and people from a file (identity-spine S01.12.3), with its tests in the pull request checks, published to GitHub Container Registry as `ghcr.io/supermanchris4/ai-app-platform-mock-idp`, scanned with Trivy.

### S03.2.5 First release

As an application developer, I want a first release of each package and instructions for using them, so that identity-spine can switch to them (F01.11).

**Acceptance criteria:** a release workflow that publishes a package when its tag is pushed (`api-v{version}`, `web-v{version}`, `mock-idp-v{version}`), version `0.1.0` of each released, and the README's instructions for installing each, locally and in an application's pipelines ([decision 4](../README.md#decisions-needed)).
