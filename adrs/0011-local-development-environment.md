# ADR-0011: Local development environment

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Should

## Question

How do developers run and test an application on their own machine: containers in WSL, tools installed directly on Windows, or a cloud-hosted development environment?

## Why it matters

The answer decides what the template ships for local development: a Docker Compose file, a Windows setup guide or a cloud environment definition. It also decides how closely local testing matches the deployed Linux containers.

## Options

| Option | For | Against |
|---|---|---|
| A. WSL with Docker Engine inside it | No licence cost. Local containers match the deployed ones. | Needs WSL and administrator rights to install it |
| B. WSL with Docker Desktop | Simpler install and a graphical interface | Docker Desktop's licence requires a paid subscription for larger organisations |
| C. Tools installed directly on Windows, without containers | No WSL or Docker | Local PostgreSQL and language runtimes differ from the deployed Linux containers, and each setup drifts |
| D. Cloud-hosted development environment, such as GitHub Codespaces | Nothing to install locally. Every developer gets an identical environment. | Cost, and depends on network access to the service |

## Decision

A. Native Linux or macOS with a Docker-compatible engine works the same way.

### Working practices for option A

Source code is kept in the WSL Linux filesystem (for example `~/src`), not on the Windows drive under `/mnt/c`. Containers receive file-change events only for files stored in the Linux filesystem, and access is much faster there. WSL is kept up to date. Sources: [Docker: WSL best practices](https://docs.docker.com/desktop/features/wsl/best-practices/), [Docker blog: WSL 2 best practices](https://www.docker.com/blog/docker-desktop-wsl-2-best-practices/).
