# F03.3 Infrastructure modules and workflows

Part of [E03 Platform code](README.md) in the [work plan](../README.md). An enabler Feature: the first application's Terraform modules and reusable GitHub Actions workflows move into `ai-app-platform` and are released by tag, so every application deploys with the same tested infrastructure and pipelines instead of writing its own.

## Benefits

- An application's Terraform composes released modules, and its workflows call released ones, so it writes only what is its own: its Terraform root, its settings and the workflows that call the shared ones.
- A fix to a module or a workflow reaches every application as a new version.

## Acceptance criteria

1. The 23 Terraform modules from the first application's `infra/modules/`, with their tests, are in `ai-app-platform`, unchanged apart from documentation, and pass `terraform fmt`, `validate` and `test` and Trivy's misconfiguration scan in its pull request checks.
2. The reusable workflows from the first application (the pull request checks, Terraform checks, plan and apply, and the application build and deployment) are in `ai-app-platform`'s `.github/workflows/`, documented with their inputs, and checked with actionlint.
3. The Terraform outputs the deployment workflow reads from an application's Terraform root are documented as a contract.
4. The release workflow releases the modules at `terraform-v{version}` and the workflows at `workflows-v{version}`, each with a changelog, and version 0.1.0 of each is released.
5. Each README says how an application takes them: a module by its git source and a workflow by its path, each at the commit SHA the release tag names with the tag in a comment, as the template's `13-delivery-pipeline.md` requires of every action. The READMEs say to take the commit only from the release or the tag, since a public repository's address also serves commits pushed to its forks.
6. The template's specifications say what the modules and workflows provide and what the application still writes, replacing F02.5's "until F03.3" notes.

Nothing is applied to an AWS account: Claude validates and tests the modules, and they are first applied through an application's pipeline, when the first application adopts them.

## Dependencies

- [F03.2](f03-2-platform-repository.md), done: the repository, its checks and its release workflow.
- The first application's Terraform and workflows, done: the modules and workflows exist there, checked but never applied.

## Not in this Feature

Found while scoping it on 2026-11-13, and left for later Features if wanted:

- **The application's Terraform root as a module.** The first application's `infra/aws/` composes the modules and also holds what is its own (its upload bucket, its settings). Its outputs are the deployment workflow's contract (criterion 3); a composition module would guarantee them, but needs a second application to show what varies.
- **The account bootstrap.** The first application's `infra/bootstrap/` is a root an operator runs once; it composes released modules after F03.3.
- **The database initialisation job.** `jobs/initialise_database.py`, which creates the database users, is generic but lives in the first application's API. It belongs in the API platform package.

## Stories

### S03.3.1 Terraform modules

As an application developer, I want the infrastructure modules released from `ai-app-platform`, so that my Terraform composes tested modules.

**Acceptance criteria:** the Feature's acceptance criterion 1. `packages/terraform/modules/` holds the modules, each with a README naming its inputs and outputs; the pull request checks run the copied `terraform-checks.yml` over them, as the "Terraform modules / Checks" check.

### S03.3.2 Reusable workflows

As an application developer, I want the pipeline workflows released from `ai-app-platform`, so that my workflows call tested ones.

**Acceptance criteria:** the Feature's acceptance criteria 2 and 3. `build-application`, `deploy-application`, `terraform-plan`, `terraform-apply`, `terraform-checks` and `contract-check` are added, and `python-checks`, `web-checks`, `image-checks` and `security-checks` are brought up to the first application's versions (including `web-checks`' `github-packages-scope` input). A `workflows/README.md` documents each workflow's inputs and the Terraform outputs contract, and the pull request checks run actionlint.

### S03.3.3 Release the modules and workflows

As an application developer, I want versioned releases of the modules and workflows, so that I upgrade them on purpose.

**Acceptance criteria:** the Feature's acceptance criteria 4 and 5.

### S03.3.4 Reference them in the template

As someone creating an application, I want the template to say which modules and workflows to take, so that I write only my own Terraform root and calling workflows.

**Acceptance criteria:** the Feature's acceptance criterion 6: `10-architecture.md`'s platform packages, `12-aws-deployment.md`, `13-delivery-pipeline.md`, `TEMPLATE_USAGE.md` and `infra/README.md`.
