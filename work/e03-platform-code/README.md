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

- identity-spine's rearchitecture, for F03.1, and its Terraform and workflows (identity-spine F01.9 and F01.10), for F03.3.
- [Decision 1](../README.md#decisions-needed), for F03.2.

## Features

| Feature | Type | Horizon | Detail |
|---|---|---|---|
| F03.1 Platform code in identity-spine | Enabler | Now | [f03-1-platform-code-in-identity-spine.md](f03-1-platform-code-in-identity-spine.md) |
| F03.2 Platform repository | Enabler | Now | [f03-2-platform-repository.md](f03-2-platform-repository.md) |
| F03.3 Infrastructure modules and workflows | Enabler | Next | The Terraform for each concern in the template's `12-aws-deployment.md`, and the pull request, infrastructure and deployment workflows in `13-delivery-pipeline.md`, as reusable modules and workflows. Like the platform modules, they are built first in identity-spine (identity-spine F01.9 and F01.10) and moved here, and identity-spine then consumes them by tag (identity-spine F01.13). Claude validates them with `fmt` and `validate`; they are first applied through a pipeline. |
| F03.4 Directory lookup | Enabler | Later | The optional directory lookup endpoint in the API platform, as the template's `20-api-scaffold.md` specifies ([D-10](../../docs/decision-register.md#d-10-directory-lookups)) |

## Extraction work found in identity-spine

Work F03.2 must do beyond moving files, found while checking identity-spine's platform modules on 2026-11-01. identity-spine F01.12 settles which code is platform code first; what is left is packaging.

| Area | Today in identity-spine | Work in F03.2 |
|---|---|---|
| Import paths | The API's platform modules are top-level modules imported by bare name (`from settings import`, `from auth.context import`, `from database import`, `from middleware.correlation import`, `from logging_config import`) | Put them under one package namespace, and change the application's imports to match |
| Declarative base | `auth/sessions.py` declares `AuthSession` on the application's `database.Base`, so the platform and the application share one SQLAlchemy metadata | Decide how the package's tables join the application's metadata, so the application's Alembic still sees them, for example by the package owning `Base` and the application importing it |
| Migrations | The `auth_sessions` table is created by migration `0012_auth_sessions.py` in the application's Alembic history; migrations call `grant_runtime` from `migration_grants.py` | Decide how the package ships its tables' schema: its own Alembic version location that the application adds, or migrations the application writes from the package's models. `migration_grants.py` moves with the package if its tables need it |
| Tests | Platform tests sit among the application's in `apps/api/tests/unit/` (for example `test_auth_tokens.py`, `test_web_sign_in.py`, `test_settings.py`), and the test token issuer is `tests/issuer.py` | Move them to the package, run them in its pull request checks, and release the issuer as a test helper the application imports |
| Web platform | `apps/web/src/platform/` is a folder in the application's `@identity-spine/web` package | Build it as a library with `react` and `react-router` as peer dependencies, and publish it as a scoped package |
| Mock identity provider | `infra/local/mock-idp/` already has its own `pyproject.toml`, lock file, tests and `Dockerfile` | Publish the image; identity-spine S01.12.3 first moves the application's roles and people into configuration |

