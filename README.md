# AI application standard

## Purpose

The standard for applications built with the help of AI coding assistants, such as Claude Code: the architecture, the decisions behind it and the development process. It suits the typical shape of these applications: a web user interface, an API running in a container and a PostgreSQL database, hosted on AWS with source code and deployment pipelines in GitHub.

The standard is implemented by a separate starter repository, `ai-app-template`. An application is created from the template and then owns its copy, with the structure, configuration, pipelines and instructions for the AI coding assistant already in place. The standard is kept here rather than in the template so that it is held once. If it lived in the template, every application would hold its own copy and the copies would drift apart. The template implements the standard and refers to it rather than repeating it. The platform code every application needs is released as versioned packages from a third repository, `ai-app-platform`, which applications install rather than copy ([ADR-0001](adrs/0001-shape-of-the-outcomes.md)).

## Documents

| Document | Contents |
|---|---|
| [docs/design.md](docs/design.md) | The architecture and development practices the template implements |
| [docs/configuration-defaults.md](docs/configuration-defaults.md) | The settings the template reads as configuration, with their defaults, and the remaining assumptions |
| [docs/developer-setup.md](docs/developer-setup.md) | Setting up a workstation to build applications from the template |
| [adrs/](adrs/) | The decisions behind the design, one architecture decision record (ADR) per decision with the options considered and the outcome, listed in the [index](adrs/README.md) |

The [work/](work/) folder holds the [work plan](work/README.md) for completing the standard and the template and building the shared platform code, with the status of each item in its [tracker](work/tracker.md). It is not part of the standard.

## Licence

Apache License 2.0: see [LICENSE](LICENSE).
