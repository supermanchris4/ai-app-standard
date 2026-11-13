# E03 Platform code

Part of the [work plan](../README.md). An enabler Epic: it builds the code every application built from the template needs, first inside the first application, then as shared, versioned packages.

## Pitch

Every application built from the template needs sign-in, an API platform with settings, logging, errors, health, database access and audit, a web shell, a local identity provider, Terraform modules and pipelines. The template specifies all of these but holds no code. This Epic builds them once, in the first application, in modules kept separate from its domain code ([ADR-0001](../../adrs/0001-shape-of-the-outcomes.md)). When a second application needs them, they move to their own repository, tested against the template specifications and released by version tag.

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
3. The first application consumes the extracted packages by version tag.

## Dependencies

- The first application's rearchitecture, for F03.1, and its Terraform and workflows, for F03.3.
- [Decision 1](../README.md#decisions-needed), for F03.2.

## Features

| Feature | Type | Horizon | Detail |
|---|---|---|---|
| F03.1 Platform code in the first application | Enabler | Now | [f03-1-platform-code-in-the-first-application.md](f03-1-platform-code-in-the-first-application.md) |
| F03.2 Platform repository | Enabler | Now | [f03-2-platform-repository.md](f03-2-platform-repository.md) |
| F03.3 Infrastructure modules and workflows | Enabler | Now | [f03-3-infrastructure-modules-and-workflows.md](f03-3-infrastructure-modules-and-workflows.md) |
| F03.5 External users | Enabler | Later | External users in the API and web platform packages, as the template's `50-auth.md` specifies: the external issuer's sign-in, sessions and bearer tokens with the `external` actor type, and the web platform's sign-in for either provider. Version 0.1 has the external issuer's settings and discovery only; the first application has no external users. Found by F02.5 on 2026-11-12 |
| F03.4 Directory lookup | Enabler | Later | The optional directory lookup endpoint in the API platform, as the template's `20-api-scaffold.md` specifies ([ADR-0010](../../adrs/0010-directory-lookups.md)) |
| F03.6 Traceability check | Enabler | Next | A reusable workflow and its script in `ai-app-platform` for the template's Traceability pull request check (`13-delivery-pipeline.md`): every requirement ID a specification, a test or a register cites exists in `02-requirements.md`, every test names the requirements it verifies, and the generated requirements trace report is current. The template's `ci.yml` calls it once released. |

## Extraction work found in the first application

Work F03.2 must do beyond moving files, found while checking the first application's platform modules on 2026-11-01. The first application's delivery plan settles which code is platform code first; what is left is packaging.

| Area | Today in the first application | Work in F03.2 |
|---|---|---|
| Import paths | The API's platform modules are top-level modules imported by bare name (`from settings import`, `from auth.context import`, `from database import`, `from middleware.correlation import`, `from logging_config import`) | Put them under one package namespace, and change the application's imports to match |
| Declarative base | `auth/sessions.py` declares `AuthSession` on the application's `database.Base`, so the platform and the application share one SQLAlchemy metadata | Decide how the package's tables join the application's metadata, so the application's Alembic still sees them, for example by the package owning `Base` and the application importing it |
| Migrations | The `auth_sessions` table is created by migration `0012_auth_sessions.py` in the application's Alembic history; migrations call `grant_runtime` from `migration_grants.py` | Decide how the package ships its tables' schema: its own Alembic version location that the application adds, or migrations the application writes from the package's models. `migration_grants.py` moves with the package if its tables need it |
| Tests | Platform tests sit among the application's in `apps/api/tests/unit/` (for example `test_auth_tokens.py`, `test_web_sign_in.py`, `test_settings.py`), and the test token issuer is `tests/issuer.py` | Move them to the package, run them in its pull request checks, and release the issuer as a test helper the application imports |
| Web platform | `apps/web/src/platform/` is a folder in the application's own web package | Build it as a library with `react` and `react-router` as peer dependencies, and publish it as a scoped package |
| Mock identity provider | `infra/local/mock-idp/` already has its own `pyproject.toml`, lock file, tests and `Dockerfile` | Publish the image; the first application first moves its roles and people into configuration |

