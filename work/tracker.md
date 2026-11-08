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
| [F02.5](e02-template-specifications/README.md#f025-platform-code-references) | Feature | Platform code references | E02 | Next | Not started | | |
| [F02.6](e02-template-specifications/README.md#features) | Feature | Template proof | E02 | Later | Not started | | |
| [E03](e03-platform-code/README.md) | Epic | Platform code | | | In progress | | |
| [F03.1](e03-platform-code/f03-1-platform-code-in-identity-spine.md) | Feature | Platform code in identity-spine | E03 | Now | In progress | | Delivered by identity-spine's rearchitecture; acceptance criterion 2 awaits identity-spine F01.12 (module boundaries) |
| [S03.1.1](e03-platform-code/f03-1-platform-code-in-identity-spine.md#s0311-build-the-api-platform-module) | Story | Build the API platform module | F03.1 | Now | In progress | | Built in identity-spine F01.3, F01.4 and F01.8. Rate limiting moves out of the platform middleware, and the audit service moves into it (identity-spine decision 8), both in identity-spine F01.12 |
| [S03.1.2](e03-platform-code/f03-1-platform-code-in-identity-spine.md#s0312-build-the-sign-in-and-access-module) | Story | Build the sign-in and access module | F03.1 | Now | Done | | identity-spine F01.5 |
| [S03.1.3](e03-platform-code/f03-1-platform-code-in-identity-spine.md#s0313-build-the-local-mock-identity-provider) | Story | Build the local mock identity provider | F03.1 | Now | In progress | | Built in identity-spine S01.5.1; the application's roles and people move into configuration in identity-spine S01.12.3 |
| [S03.1.4](e03-platform-code/f03-1-platform-code-in-identity-spine.md#s0314-build-the-web-platform-module) | Story | Build the web platform module | F03.1 | Now | Done | | identity-spine S01.7.1. The component kit is not part of it (identity-spine decision 9) |
| [F03.2](e03-platform-code/f03-2-platform-repository.md) | Feature | Platform repository | E03 | Now | In progress | | Decision 1 decided 2026-11-08: extract now, into `ai-app-platform` |
| [S03.2.1](e03-platform-code/f03-2-platform-repository.md#s0321-repository-foundation) | Story | Repository foundation | F03.2 | Now | Not started | |  |
| [S03.2.2](e03-platform-code/f03-2-platform-repository.md#s0322-api-platform-package) | Story | API platform package | F03.2 | Now | Not started | | |
| [S03.2.3](e03-platform-code/f03-2-platform-repository.md#s0323-web-platform-package) | Story | Web platform package | F03.2 | Now | Not started | |  |
| [S03.2.4](e03-platform-code/f03-2-platform-repository.md#s0324-mock-identity-provider-image) | Story | Mock identity provider image | F03.2 | Now | Not started | |  |
| [S03.2.5](e03-platform-code/f03-2-platform-repository.md#s0325-first-release) | Story | First release | F03.2 | Now | Not started | | |
| [F03.3](e03-platform-code/README.md#features) | Feature | Infrastructure modules and workflows | E03 | Next | Not started | | After F03.2, into the same repository |
| [F03.4](e03-platform-code/README.md#features) | Feature | Directory lookup | E03 | Later | Not started | | |
