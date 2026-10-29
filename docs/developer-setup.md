# Developer setup

Setting up a Windows workstation to build applications from the template, following [D-11](decision-register.md#d-11-local-development-environment) and [D-14](decision-register.md#d-14-developer-access-to-aws). On native Linux or macOS, skip the WSL steps and install the same tools.

## 1. WSL

1. In an administrator PowerShell, run `wsl --install -d Ubuntu`, then restart.
2. Open Ubuntu, create the Linux user, and run `sudo apt update && sudo apt upgrade -y`.
3. Keep WSL current with `wsl --update` from PowerShell.

All source code lives in the Linux filesystem, for example `~/src`, never under `/mnt/c`. The template's `CLAUDE.md` stops Claude Code from writing code anywhere else.

## 2. Docker Engine in WSL

Install Docker Engine inside Ubuntu, not Docker Desktop:

1. Follow [Docker's Ubuntu instructions](https://docs.docker.com/engine/install/ubuntu/) to add Docker's apt repository and install `docker-ce`, `docker-ce-cli`, `containerd.io`, `docker-buildx-plugin` and `docker-compose-plugin`.
2. Run `sudo usermod -aG docker $USER`, then close and reopen the terminal.
3. Enable systemd in WSL if it is not already on: add `[boot]` and `systemd=true` to `/etc/wsl.conf`, then run `wsl --shutdown` from PowerShell.
4. Check with `docker run --rm hello-world` and `docker compose version`.

Ports published by containers in WSL are reachable from Windows on `localhost`. If they are not, check that `localhostForwarding=true` is set under `[wsl2]` in `%UserProfile%\.wslconfig`.

## 3. Languages and tools

| Tool | Install | Check |
|---|---|---|
| git | `sudo apt install -y git`, then set `user.name` and `user.email` | `git --version` |
| uv | `curl -LsSf https://astral.sh/uv/install.sh \| sh` | `uv --version` |
| Node | `nvm` from [its install instructions](https://github.com/nvm-sh/nvm), then `nvm install` in a repository (it reads `.nvmrc`) | `node --version` |
| pre-commit | `uv tool install pre-commit`, then `pre-commit install` in each clone | `pre-commit --version` |
| GitHub CLI | [GitHub CLI for Ubuntu](https://github.com/cli/cli/blob/trunk/docs/install_linux.md), then `gh auth login` | `gh auth status` |
| Terraform | [HashiCorp's apt repository](https://developer.hashicorp.com/terraform/install) | `terraform version` |
| AWS CLI v2 | [AWS CLI install for Linux](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) | `aws --version` |
| Claude Code | [Claude Code setup](https://docs.claude.com/en/docs/claude-code/setup) | `claude --version` |

Python itself comes from uv, which installs the version an application's `pyproject.toml` requires.

## 4. AWS access

AWS access uses IAM Identity Center single sign-on. No access keys are stored on the workstation.

1. Run `aws configure sso`, giving the Identity Center start URL and region, and choose the development account and permission set. Name the profile after the account, for example `dev`.
2. Sign in with `aws sso login --profile dev`. If the browser does not open from WSL, add `--use-device-code`.
3. Check with `aws sts get-caller-identity --profile dev`.

Test and production accounts are changed only through the pipelines.

## 5. Editor

VS Code on Windows with the WSL extension opens folders in the Linux filesystem (`code ~/src/my-app` from Ubuntu). The Claude Code extension then runs inside WSL.

## 6. First run of an application

From the repository root, follow the commands in the application's `infra/local/README.md`: copy `.env.example` to `.env`, start the containers, apply the migrations and start the web interface.
