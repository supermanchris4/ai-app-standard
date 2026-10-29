# Roadmap

What remains to be done on the standard and the template. Remove an item once it is done.

| Item | What it involves |
|---|---|
| ADRs for the decisions | Write ADR-0001 to ADR-0019 from the [decision register](docs/decision-register.md), then shrink each register entry to its title, status and a link |
| Platform code | Build the platform code (settings, logging, sign-in and sessions, the mock identity provider, the web shell, Terraform modules and reusable workflows) in the first application, in separate modules. Extract it into a shared, versioned platform repository once a second application needs it ([D-01](docs/decision-register.md#d-01-shape-of-the-outcomes)), and update the template's specifications to name the package that provides each part. |
| Template proof | Create an application from the template and deploy it end to end to AWS, fixing any gaps in the template's specifications |
| Bootstrap network | Specify the optional minimal network the bootstrap creates for accounts without one ([D-05](docs/decision-register.md#d-05-network-placement)) |
| GitHub plan features | Confirm which push protection and environment protection features the account's plan provides on private repositories ([A-02](docs/configuration-defaults.md#a-02)) |
| Pyright language server | Assess Claude Code's `pyright-lsp` plugin against a project environment, and enable it in the template's `.claude/settings.json` if it works |
