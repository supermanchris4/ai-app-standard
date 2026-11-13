# Tracker

The status of every Epic, Feature and Story in the [work plan](README.md), and each item's GitHub issue where it has one. This is the only place status is recorded. Tasks are tracked with their Story: a Story is Done when all its Tasks are.

| Status | Meaning |
|---|---|
| Not started | No work has begun |
| In progress | Work has begun |
| Blocked | Work cannot continue until what the note names is in place |
| Done | The acceptance criteria are met |

| ID | Level | Title | Parent | Horizon | Status | Issue | Note |
|---|---|---|---|---|---|---|---|
| [E01](e01-standard-completion/README.md) | Epic | Standard completion | | | Not started | | |
| [F01.1](e01-standard-completion/f01-1-adrs.md) | Feature | ADRs | E01 | Now | Not started | | |
| [S01.1.1](e01-standard-completion/f01-1-adrs.md#s0111-record-the-platform-decisions) | Story | Record the platform decisions | F01.1 | Now | Not started | | |
| [S01.1.2](e01-standard-completion/f01-1-adrs.md#s0112-record-the-aws-and-pipeline-decisions) | Story | Record the AWS and pipeline decisions | F01.1 | Now | Not started | | |
| [S01.1.3](e01-standard-completion/f01-1-adrs.md#s0113-record-the-remaining-decisions) | Story | Record the remaining decisions | F01.1 | Now | Not started | | |
| [S01.1.4](e01-standard-completion/f01-1-adrs.md#s0114-shrink-the-register) | Story | Shrink the register | F01.1 | Now | Not started | | |
| [F01.2](e01-standard-completion/f01-2-open-checks.md) | Feature | Open checks | E01 | Now | Not started | | |
| [S01.2.1](e01-standard-completion/f01-2-open-checks.md#s0121-github-plan-features-on-private-repositories) | Story | GitHub plan features on private repositories | F01.2 | Now | Not started | | |
| [S01.2.2](e01-standard-completion/f01-2-open-checks.md#s0122-pyright-language-server-plugin) | Story | Pyright language server plugin | F01.2 | Now | Not started | | |
| [E02](e02-template-specifications/README.md) | Epic | Template specifications | | | In progress | | |
| [F02.1](e02-template-specifications/README.md#features) | Feature | Specification baseline | E02 | Done | Done | | |
| [F02.2](e02-template-specifications/README.md#features) | Feature | Repository settings and versions | E02 | Done | Done | | |
| [F02.3](e02-template-specifications/README.md#features) | Feature | Guides | E02 | Done | Done | | |
| [F02.4](e02-template-specifications/f02-4-bootstrap-network.md) | Feature | Bootstrap network | E02 | Now | Not started | | |
| [S02.4.1](e02-template-specifications/f02-4-bootstrap-network.md#s0241-specify-the-minimal-network) | Story | Specify the minimal network | F02.4 | Now | Not started | | |
| [F02.5](e02-template-specifications/f02-5-platform-code-references.md) | Feature | Platform code references | E02 | Now | Done | | `ai-app-template` pull request #1 and `ai-app-standard` pull request #6, merged 2026-11-12. The gaps it found became F03.5 External users |
| [S02.5.1](e02-template-specifications/f02-5-platform-code-references.md#s0251-reference-the-platform-packages-in-the-specifications) | Story | Reference the platform packages in the specifications | F02.5 | Now | Done | | |
| [S02.5.2](e02-template-specifications/f02-5-platform-code-references.md#s0252-show-what-an-application-writes) | Story | Show what an application writes | F02.5 | Now | Done | | |
| [S02.5.3](e02-template-specifications/f02-5-platform-code-references.md#s0253-explain-how-to-take-the-packages) | Story | Explain how to take the packages | F02.5 | Now | Done | | The API platform installs from its git tag: Dependabot cannot check a release wheel's signed download link |
| [S02.5.4](e02-template-specifications/f02-5-platform-code-references.md#s0254-describe-the-platform-repository-as-an-outcome) | Story | Describe the platform repository as an outcome | F02.5 | Now | Done | | |
| [F02.6](e02-template-specifications/README.md#features) | Feature | Template proof | E02 | Later | Not started | | |
| [E03](e03-platform-code/README.md) | Epic | Platform code | | | In progress | | |
| [F03.1](e03-platform-code/f03-1-platform-code-in-the-first-application.md) | Feature | Platform code in the first application | E03 | Now | Done | | Delivered by the first application's rearchitecture, with its module boundaries settled before extraction |
| [S03.1.1](e03-platform-code/f03-1-platform-code-in-the-first-application.md#s0311-build-the-api-platform-module) | Story | Build the API platform module | F03.1 | Now | Done | | Built in the first application. Rate limiting stayed in the application, and the audit service moved into the platform code, before extraction |
| [S03.1.2](e03-platform-code/f03-1-platform-code-in-the-first-application.md#s0312-build-the-sign-in-and-access-module) | Story | Build the sign-in and access module | F03.1 | Now | Done | | Built in the first application |
| [S03.1.3](e03-platform-code/f03-1-platform-code-in-the-first-application.md#s0313-build-the-local-mock-identity-provider) | Story | Build the local mock identity provider | F03.1 | Now | Done | | Built in the first application; the application's roles and people moved into configuration before extraction |
| [S03.1.4](e03-platform-code/f03-1-platform-code-in-the-first-application.md#s0314-build-the-web-platform-module) | Story | Build the web platform module | F03.1 | Now | Done | | Built in the first application. The component kit is not part of it |
| [F03.2](e03-platform-code/f03-2-platform-repository.md) | Feature | Platform repository | E03 | Now | Done | | Decision 1 decided 2026-11-08: extract now, into `ai-app-platform` |
| [S03.2.1](e03-platform-code/f03-2-platform-repository.md#s0321-repository-foundation) | Story | Repository foundation | F03.2 | Now | Done | | `ai-app-platform` pull request #1: README, CLAUDE.md, pre-commit with gitleaks, Dependabot, CI with the security checks. The `main` ruleset is imported and enforced, and secret scanning, push protection and Dependabot alerts are on |
| [S03.2.2](e03-platform-code/f03-2-platform-repository.md#s0322-api-platform-package) | Story | API platform package | F03.2 | Now | Done | | `ai-app-platform` pull request #2: `packages/api`, the first application's `ai_app_platform` unchanged, with `create_platform_tables`, its tests against PostgreSQL through a minimal test application (92% coverage), and a check that it imports only what it declares |
| [S03.2.3](e03-platform-code/f03-2-platform-repository.md#s0323-web-platform-package) | Story | Web platform package | F03.2 | Now | Done | | `ai-app-platform` pull request #3: `packages/web`, the first application's web platform modules unchanged, built by Vite as a library with React and React Router as peer dependencies and type declarations, tested with Vitest (98% coverage). Publishing comes with S03.2.5 |
| [S03.2.4](e03-platform-code/f03-2-platform-repository.md#s0324-mock-identity-provider-image) | Story | Mock identity provider image | F03.2 | Now | Done | | `ai-app-platform` pull request #7: `packages/mock-idp` with an example roles and people file, its tests, and its image built and scanned with Trivy in the pull request checks; pip is removed from the image |
| [S03.2.5](e03-platform-code/f03-2-platform-repository.md#s0325-first-release) | Story | First release | F03.2 | Now | Done | | `ai-app-platform` pull request #11: `release.yml` publishes each package from its version tag. Released 2026-11-10: `api-v0.1.0` (git tag, wheel on the release), `web-v0.1.0` (GitHub Packages), `mock-idp-v0.1.0` (public image on GitHub Container Registry), each with a CHANGELOG and install instructions |
| [F03.3](e03-platform-code/README.md#features) | Feature | Infrastructure modules and workflows | E03 | Next | Not started | | After F03.2, into the same repository |
| [F03.4](e03-platform-code/README.md#features) | Feature | Directory lookup | E03 | Later | Not started | | |
| [F03.5](e03-platform-code/README.md#features) | Feature | External users | E03 | Later | Not started | | Found by F02.5: the packages support internal users only |
