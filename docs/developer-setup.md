# Developer setup

How to set up a Windows workstation to build an application from the `ai-app-template` repository. The setup runs Linux on the workstation through WSL (Windows Subsystem for Linux), with Docker Engine and the language tools installed inside it. Source code is kept in the Linux filesystem. GitHub and AWS are reached without a long-lived credential stored in a file. On native Linux or macOS, skip the WSL steps and install the same tools.

The reasons for the setup are in [ADR-0011](../adrs/0011-local-development-environment.md) (WSL with Docker Engine) and [ADR-0014](../adrs/0014-developer-access-to-aws.md) (AWS access). How the template runs the application once the workstation is set up is in its `specs/11-local-development.md`.

Run commands in a WSL terminal unless the step says PowerShell.

## Before you start

| You need | From |
|---|---|
| Administrator rights on the workstation, to install WSL | Whoever manages the workstation |
| A GitHub account with access to the application's repository | The repository's owner |
| Access to the application's AWS development account through IAM Identity Center, and its start URL | Whoever administers the AWS accounts |

VS Code with its WSL extension and Git for Windows must also be installed on Windows.

## 1. Install WSL

In PowerShell as administrator:

```powershell
wsl --install -d Ubuntu-26.04
wsl --update
```

`wsl --list --online` shows the distributions available. Choose the latest Ubuntu LTS. Restart when asked, then open Ubuntu from the Start menu and create your Linux user. Keep WSL current with `wsl --update`.

Check that systemd is on, because Docker Engine runs as a systemd service. `/etc/wsl.conf` should contain:

```ini
[boot]
systemd=true
```

If you add it, run `wsl --shutdown` in PowerShell and reopen Ubuntu.

Ports published by containers in WSL are reachable from Windows on `localhost`. If a VPN or network security client on the workstation stops WSL reaching the network, create `%USERPROFILE%\.wslconfig` on Windows with only this content, then run `wsl --shutdown` in PowerShell and reopen Ubuntu:

```ini
[wsl2]
networkingMode=virtioproxy
```

Leave `/etc/resolv.conf` as WSL generates it. A hand-written one breaks name resolution when the workstation moves between networks.

## 2. Trust your network's certificate

Skip this step unless your network inspects encrypted traffic. Some networks do, re-signing it with their own root certificate. Windows trusts that certificate but WSL does not, so downloads from inside WSL fail with certificate errors until it is added. `curl -sI https://pypi.org | head -1` in WSL shows whether this applies: a certificate error means it does.

Export the certificate from the Windows store in PowerShell, replacing `<issuer>` with part of the root certificate's subject, which whoever manages the network can give you:

```powershell
Get-ChildItem Cert:\LocalMachine\Root |
  Where-Object Subject -like '*<issuer>*' |
  Export-Certificate -FilePath "$env:USERPROFILE\network-root.cer"
```

Add it to the Linux store in WSL, replacing `<windows-user>` with your Windows user name:

```bash
sudo openssl x509 -inform der \
  -in /mnt/c/Users/<windows-user>/network-root.cer \
  -out /usr/local/share/ca-certificates/network-root.crt
sudo update-ca-certificates
curl -sI https://pypi.org | head -1
```

The last command should print a status line ending in `200`.

Node and uv keep their own lists of trusted certificates. Add these lines to the end of `~/.bashrc` so they use the Linux store, then run `exec bash`:

```bash
# TLS inspection: tools with their own CA store use the system bundle,
# which holds the network's root after update-ca-certificates
export UV_SYSTEM_CERTS=1
export NODE_EXTRA_CA_CERTS=/etc/ssl/certs/ca-certificates.crt
```

When the network's root certificate is replaced, repeat the export and `update-ca-certificates`, then run `sudo systemctl restart docker`. Each application repository also keeps a copy of the bundle for its image builds; copy it again as the repository's local development commands describe.

## 3. Install Docker Engine

Install Docker Engine from Docker's own package repository, as [Docker's Ubuntu instructions](https://docs.docker.com/engine/install/ubuntu/) describe. Do not install Docker Desktop, because its licence needs a paid subscription for larger organisations ([ADR-0011](../adrs/0011-local-development-environment.md)).

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
sudo tee /etc/apt/sources.list.d/docker.sources > /dev/null <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Let your user run Docker without `sudo`, then run `wsl --shutdown` in PowerShell and reopen Ubuntu so the change takes effect:

```bash
sudo usermod -aG docker "$USER"
```

Membership of the `docker` group gives root-level control of WSL. That is acceptable for a development environment that holds no real secrets.

Check that containers run and that images download:

```bash
docker run --rm hello-world
```

## 4. Install the language tools

Install Node through nvm, so that each application's `.nvmrc` can select its version. Ubuntu's own Node package is too old. Take the install command for the latest release from the [nvm repository](https://github.com/nvm-sh/nvm#installing-and-updating); this shows v0.40.8:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.8/install.sh | bash
exec bash
nvm install --lts
nvm alias default 'lts/*'
```

Install uv, which manages Python and the API's dependencies. Take the latest version from the [uv installation page](https://docs.astral.sh/uv/getting-started/installation/); this shows 0.13.6:

```bash
curl -LsSf https://astral.sh/uv/0.13.6/install.sh | sh
exec bash
```

Do not install Python separately. uv installs the version the application's `pyproject.toml` requires the first time it is needed.

Install pre-commit, which runs the secret scan before each commit:

```bash
uv tool install pre-commit
```

Install the remaining tools:

| Tool | Install | Check |
|---|---|---|
| GitHub CLI | [GitHub CLI for Ubuntu](https://github.com/cli/cli/blob/trunk/docs/install_linux.md), then `gh auth login` | `gh auth status` |
| Terraform | [HashiCorp's apt repository](https://developer.hashicorp.com/terraform/install) | `terraform version` |
| Claude Code | [Claude Code setup](https://docs.claude.com/en/docs/claude-code/setup) | `claude --version` |

## 5. Set up Git and GitHub

Git is part of Ubuntu. Set your name and the email address you commit with:

```bash
git config --global user.name "<Your Name>"
git config --global user.email "<you>@example.com"
```

Use Git Credential Manager from Git for Windows to sign in to GitHub. It holds your sign-in in Windows Credential Manager rather than in a file in WSL.

```bash
git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/bin/git-credential-manager.exe"
```

The first time Git reaches GitHub, a browser window opens for you to sign in. Git uses this sign-in for the application's repository, and for the Python platform package and Terraform modules, which are referenced by git tag.

## 6. Keep source code in the Linux filesystem

Clone repositories into your Linux home directory, for example `~/src`, never under `/mnt/c`. Containers see file changes only for files in the Linux filesystem, and builds and tests run much faster there. The template's `CLAUDE.md` stops Claude Code from writing code anywhere else.

```bash
mkdir -p ~/src && cd ~/src
git clone https://github.com/<owner>/<repository>.git
cd <repository>
pre-commit install
code .
```

`code .` opens the repository in VS Code through the WSL extension; the bottom-left corner then shows **WSL: Ubuntu**. Extensions are installed separately for WSL: install the ones you need in the WSL window when VS Code offers, including **Python** (Microsoft), which brings Pylance. The Claude Code extension runs inside WSL. Run `pre-commit install` once in each clone.

## 7. Access the platform packages

The web platform package is in GitHub Packages, whose npm registry needs a classic personal access token even for a public package. Your Git sign-in does not cover it. The local mock identity provider image is in GitHub Container Registry and needs the same token only if the platform's images are private.

Create a classic personal access token in GitHub with only the `read:packages` scope, and with the shortest expiry you can work with. No other scope is needed.

Have Git Credential Manager hold credentials for the package registry's host. The settings apply to that host only, so they work whether Git's own sign-in to `github.com` goes through Git Credential Manager (step 5) or through the GitHub CLI. `provider generic` makes it store and return the token as given, rather than treating the host as GitHub and starting its own sign-in, and `allowWindowsAuth false` stops it probing the host, which answers 404 and makes it fail:

```bash
git config --global credential.https://npm.pkg.github.com.helper "/mnt/c/Program\ Files/Git/mingw64/bin/git-credential-manager.exe"
git config --global credential.https://npm.pkg.github.com.provider generic
git config --global credential.https://npm.pkg.github.com.allowWindowsAuth false
```

Store the token in Windows Credential Manager. When prompted, enter your GitHub user name and paste the token as the password; the prompt may be a Windows dialog:

```bash
printf 'protocol=https\nhost=npm.pkg.github.com\n\n' | git credential fill | git credential approve
```

Check that it is returned without a prompt. This shows the user name but not the token:

```bash
printf 'protocol=https\nhost=npm.pkg.github.com\n\n' | GIT_TERMINAL_PROMPT=0 git credential fill | grep -v '^password='
```

Add this function to `~/.bashrc`. It reads the token from Windows Credential Manager when a tool needs it, so the token is never written to a file:

```bash
gh_packages_token() {
  printf 'protocol=https\nhost=npm.pkg.github.com\n\n' | git credential fill | sed -n 's/^password=//p'
}
```

For npm, create `~/.npmrc` with the platform's scope, which the application's README names. The file holds a reference to an environment variable, not the token:

```ini
@<platform-scope>:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${GITHUB_PACKAGES_TOKEN}
```

Set the variable for the command that needs it:

```bash
GITHUB_PACKAGES_TOKEN=$(gh_packages_token) npm --prefix apps/web ci
```

If the platform's images are private, have Docker store its sign-in in Windows Credential Manager. Download `docker-credential-wincred-<version>.windows-amd64.exe` from the [Docker credential helpers releases](https://github.com/docker/docker-credential-helpers/releases), install it as `/usr/local/bin/docker-credential-wincred.exe`, and set `~/.docker/config.json` to:

```json
{ "credsStore": "wincred.exe" }
```

Then sign in once:

```bash
gh_packages_token | docker login ghcr.io -u <github-user> --password-stdin
```

When the token expires, or after you regenerate it, create or copy the new one, remove the stored one so that Git prompts again, then repeat the store step, and the `docker login` step if you used it:

```bash
printf 'protocol=https\nhost=npm.pkg.github.com\n\n' | git credential reject
```

## 8. Access AWS

AWS access uses IAM Identity Center single sign-on and is limited to development accounts. Test and production change only through the pipelines. Never create an access key or run `aws configure` with one, and `~/.aws/credentials` should not exist.

Install the AWS CLI version 2 as [AWS's Linux instructions](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) describe:

```bash
sudo apt-get install -y unzip
curl -sSL https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip -o /tmp/awscliv2.zip
unzip -q /tmp/awscliv2.zip -d /tmp && sudo /tmp/aws/install
```

Create a profile for the development account, using its start URL and the region ([C-03](configuration-defaults.md#c-03)). Name the profile after the account, for example `dev`:

```bash
aws configure sso
```

Sign in at the start of each working session. If no browser opens from WSL, add `--use-device-code` and enter the code shown in a Windows browser:

```bash
aws sso login --profile <profile>
aws sts get-caller-identity --profile <profile>
```

## 9. First run of an application

Once per clone, from the repository root:

1. `uv sync --directory apps/api`, which installs the Python version `pyproject.toml` requires and creates `apps/api/.venv` with the locked dependencies, including the development tools.
2. Point VS Code at that environment: open a Python file, run **Python: Select Interpreter** from the command palette, choose **Enter interpreter path…** and give the full path, for example `/home/{user}/src/my-app/apps/api/.venv/bin/python`. A path relative to the repository is not accepted. The selected interpreter is shown by hovering over the `{ }` icon next to **Python** in the status bar, named after the `name` in `pyproject.toml`, for example **my-app-api (3.13.x)**.
3. Follow the commands in the application's `infra/local/README.md`: copy `.env.example` to `.env`, copy the certificate bundle, start the containers, apply the schema migrations and start the web interface.

After a dependency changes, `uv sync --directory apps/api` updates the same environment; VS Code needs no change.

## Check the setup

| Check | Command | Expected |
|---|---|---|
| WSL networking and certificate | `curl -sI https://pypi.org \| head -1` | A status line ending in `200` |
| Docker | `docker run --rm hello-world` | "Hello from Docker!" without `sudo` |
| Docker Compose | `docker compose version` | A version number |
| Node and npm | `node --version && npm view left-pad version` | A version for each, with no certificate error |
| uv | `uv run --no-project --with idna python -c "import idna"` | No error |
| Secret scan | `pre-commit run --all-files` in a clone | The gitleaks hook passes |
| Source location | `pwd` in a clone | A path under `/home`, not `/mnt/c` |
| AWS | `aws sts get-caller-identity --profile <profile>` | Your single sign-on role in the development account |
| No AWS keys | `ls ~/.aws/credentials` | No such file |
