# F02.5 Platform code references

Part of [E02 Template specifications](README.md) in the [work plan](../README.md). An enabler Feature: the template describes the platform code as packages an application takes from `ai-app-platform`, rather than code each application generates.

## Benefits

- An application created from the template knows which code to install and which to write, so the platform code is not generated again.
- A reader of each template specification sees what the platform provides and what is left to the application.

## Acceptance criteria

1. Each template specification that the platform code implements says which package provides each part, and what the application still writes. The packages' current versions are named in one place, `10-architecture.md`.
2. Where the template specifies something the packages do not yet provide, the specification says so and names the work plan item that will provide it: external users ([F03.5](../e03-platform-code/README.md#features)), the directory lookup ([F03.4](../e03-platform-code/README.md#features)), and the Terraform modules and workflows ([F03.3](../e03-platform-code/README.md#features)).
3. The template's README and its `apps/` and `infra/` folder descriptions show what an application writes and what it takes from the platform.
4. `TEMPLATE_USAGE.md` explains how to take the platform packages, on a developer's machine and in the application's pipelines.
5. [ADR-0001](../../adrs/0001-shape-of-the-outcomes.md) and the [design](../../docs/design.md) describe the platform repository as a third outcome.

## Dependencies

- [F03.2](../e03-platform-code/f03-2-platform-repository.md), done: version 0.1 of each package is released, and the first application runs on them.

## Stories

### S02.5.1 Reference the platform packages in the specifications

As an application developer, I want each template specification to say what the platform packages provide, so that I install that code and write only the rest.

**Acceptance criteria:** the Feature's acceptance criteria 1 and 2.

| Task | Description |
|---|---|
| T1 | `10-architecture.md`: a table of the platform packages, what each provides, how it is installed and its current version |
| T2 | `20-api-scaffold.md`: the directory structure split into what `ai_app_platform` provides and what the application writes; the dependencies, without `authlib`, which the package does not use; the test fixtures from `ai_app_platform.testing` |
| T3 | `30-data-model.md` and `31-audit.md`: the template tables are created by `create_platform_tables` in the application's first migration; the application attaches its entity types |
| T4 | `50-auth.md`: sign-in, sessions, tokens and roles come from the package; external users are not yet provided (F03.5) |
| T5 | `40-ui.md`: the conventions the web platform package provides, and the Tailwind setting it needs |
| T6 | `11-local-development.md`: Compose pulls the released mock identity provider, and the application supplies only its roles and people file |
| T7 | `12-aws-deployment.md` and `13-delivery-pipeline.md`: the Terraform and workflows are still written by the application until F03.3 |

### S02.5.2 Show what an application writes

As someone creating an application, I want the template's README and folder descriptions to separate my code from the platform's, so that I know where to start.

**Acceptance criteria:** the Feature's acceptance criterion 3, with `CLAUDE.md`'s repository structure matching.

### S02.5.3 Explain how to take the packages

As someone creating an application, I want `TEMPLATE_USAGE.md` to say how to install the platform packages, so that my machine and my pipelines can install them with no secret.

**Acceptance criteria:** the Feature's acceptance criterion 4: the API package from its release, the web package from GitHub Packages with a `read:packages` token on a developer's machine and the run's own token in the pipelines, and the mock identity provider image with the application's roles and people file.

### S02.5.4 Describe the platform repository as an outcome

As a reader of the standard, I want its decisions and design to describe `ai-app-platform`, so that the standard, the template and the platform are presented as one set.

**Acceptance criteria:** the Feature's acceptance criterion 5.
