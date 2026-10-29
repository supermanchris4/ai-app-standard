# F01.2 Open checks

Part of [E01 Standard completion](README.md) in the [work plan](../README.md). An enabler Feature: it makes the checks that could still change a decision or a template setting.

## Benefits

- A setting that does not work as assumed is found before an application depends on it.

## Acceptance criteria

1. Each check records its result in the entry it cites.
2. Where a result changes a decision or a template specification, the change is made.

## Dependencies

- The `ai-app-standard` and `ai-app-template` repositories on GitHub, for S01.2.1.

## Stories

### S01.2.1 GitHub plan features on private repositories

As the owner of the standard, I want to know which GitHub features the account's plan provides on private repositories, so that the pipelines rely only on what exists.

**Builds on:** [A-02](../../docs/configuration-defaults.md#a-02), [D-07](../../docs/decision-register.md#d-07-deployment-pipeline-and-environment-promotion), [D-15](../../docs/decision-register.md#d-15-github-secrets-variables-and-leak-prevention).

**Acceptance criteria:** A-02 records whether secret scanning, push protection and required reviewers on GitHub Environments are available; the template's `13-delivery-pipeline.md` states the fallback for any that are not.

| Task | Description |
|---|---|
| T1 | Check each feature on a private repository in the account |
| T2 | Record the results against A-02 and update the template if needed |

### S01.2.2 Pyright language server plugin

As a developer, I want Claude Code to see Pyright's diagnostics as it edits, so that type errors are fixed as they are made.

**Builds on:** [D-02](../../docs/decision-register.md#d-02-backend-language-and-framework).

**Acceptance criteria:** the result records how Claude Code's `pyright-lsp` plugin finds the Pyright installed in the project's environment, and the plugin is enabled in the template's `.claude/settings.json` only if it works.

| Task | Description |
|---|---|
| T1 | Assess the plugin against identity-spine's environment |
| T2 | Enable it in the template's `.claude/settings.json`, or record why not |
