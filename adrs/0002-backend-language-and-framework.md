# ADR-0002: Backend language and framework

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Must

## Question

Should the API tier be Python and FastAPI, TypeScript and Express, or TypeScript on a structured framework? Which linter, formatter and type checker go with the chosen language?

## Why it matters

Business logic belongs in the API, especially logic that is complex or must be applied consistently for every user and client. The backend language therefore carries most of an application's complexity. It also fixes the supporting tools: the schema migration tool (Alembic for Python, tools such as Prisma or Knex for TypeScript), the test framework, the Docker image and the CI steps. Supporting two backends in one template doubles the maintenance.

## Options

| Option | For | Against |
|---|---|---|
| A. Python, FastAPI | Layering, error taxonomy, audit, pagination and fixtures are well established in it. Python suits calculation-heavy, statistical and batch work. OpenAPI is generated from the code. | Two languages in one repository. Web types must be generated from OpenAPI rather than shared. |
| B. TypeScript, Express | One language end to end, with types shared directly | Express imposes no structure, so the layering would be kept by convention only |
| C. TypeScript, NestJS or Fastify | One language, with enforced structure and OpenAPI support | Rework of an existing Python scaffold with no functional gain |

## Sub-decisions

Code is written and maintained with Claude Code rather than in an editor, so the pipeline's checks are what the code is held to. Claude Code runs the same checks before it hands work over.

| # | Sub-decision | Options | Position | Status | Date |
|---|---|---|---|---|---|
| a | Linter and formatter | Ruff; Pylint with Black | Ruff. It formats as well as lints, and its `PL` rule set implements most of the Pylint rules. | Decided | 2026-10-29 |
| b | Type checker | mypy; Pyright | Pyright in strict mode, installed as the `pyright[nodejs]` development dependency so it needs no separately installed Node. FastAPI, Pydantic 2 and SQLAlchemy 2's `Mapped` types are checked without plugins, and SQLAlchemy's mypy plugin is deprecated. Pyright is also the language server behind Claude Code's Python code intelligence plugin. | Decided | 2026-10-29 |

## Decision

A, with an OpenAPI-generated TypeScript client in the web tier, Ruff as linter and formatter, and Pyright in strict mode. One default backend, with a documented deviation path. The Python development tools are sub-decisions a and b.

### If the choice depends on the application

The template carries one backend. An application that needs another language records an ADR and builds its API tier itself. The web tier, infrastructure, pipeline and specifications still come from the template, because none of them depend on the API language: the web tier talks only to the OpenAPI contract, and the pipeline builds whatever image `apps/api/Dockerfile` produces.
