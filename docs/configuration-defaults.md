# Configuration defaults

Settings the [design](design.md) depends on that vary by owner or account rather than by design. The template reads each one as configuration, so changing a value is a configuration change rather than a redesign. An application that uses a different value records it in its own repository.

## Settings

| ID | Setting | Default | Template setting it drives |
|---|---|---|---|
| <a id="c-01"></a>C-01 | Identity providers | Any OpenID Connect provider. Entra ID for internal users; Entra External ID for external users, where an application has them. | The discovery URLs and client IDs in the API settings |
| <a id="c-02"></a>C-02 | Languages, runtimes and the database | Python, Node (LTS) and PostgreSQL, at the supported versions the template pins | `pyproject.toml`, `.nvmrc`, Dockerfile base images and the Terraform database engine version |
| <a id="c-03"></a>C-03 | AWS regions | `ap-southeast-2` (Sydney) only | The Terraform region variable and its permitted list |
| <a id="c-04"></a>C-04 | Network | Supplied as inputs; the bootstrap can create a minimal network ([D-05](decision-register.md#d-05-network-placement)) | Terraform network inputs |
| <a id="c-05"></a>C-05 | AWS account model | One account per environment where practical. A single account holding `dev` alone is acceptable to start. | Whether the bootstrap creates the GitHub OIDC provider |
| <a id="c-06"></a>C-06 | Data service | Amazon RDS for PostgreSQL | The Terraform database module |
| <a id="c-07"></a>C-07 | Resource tagging | `application`, `environment`, `owner`, `managed-by` | Terraform default tags |
| <a id="c-08"></a>C-08 | Internet exposure | Internet-facing through CloudFront and AWS WAF | The ingress option ([D-06](decision-register.md#d-06-ingress-and-exposure-model)) |
| <a id="c-09"></a>C-09 | Monitoring and log retention | CloudWatch, with 30-day log retention | Log retention and alarm destinations |
| <a id="c-10"></a>C-10 | Accessibility level | WCAG 2.2 level AA | Accessibility checks in the web interface tests |
| <a id="c-11"></a>C-11 | AI coding assistant | Claude Code | `CLAUDE.md`, `.claude/` settings and the generation rules |
| <a id="c-12"></a>C-12 | Developer workstation | WSL with Docker Engine | Local development files and the setup guide |
| <a id="c-13"></a>C-13 | GitHub | Private repositories in a personal account, GitHub-hosted runners | Where the template lives and the runners named in the workflows |
| <a id="c-14"></a>C-14 | Release approval | A required reviewer on `test` and `prod` | Protection rules on the GitHub Environments |
| <a id="c-15"></a>C-15 | Encryption keys and secret rotation | AWS-managed keys; database user secrets rotated every 90 days | KMS key input and rotation schedules |
| <a id="c-16"></a>C-16 | Staff access to AWS | IAM Identity Center single sign-on | Developer setup guide and AWS CLI profiles |
| <a id="c-17"></a>C-17 | Business time zone | `Australia/Perth` | `APP_BUSINESS_TIME_ZONE` and the scheduler time zone |

## Assumptions

| ID | Assumption | If wrong | How to confirm |
|---|---|---|---|
| <a id="a-01"></a>A-01 | Web interfaces have modest load (tens to low hundreds of concurrent users); API load from other systems depends on each application | Changes default sizing | Each application's non-functional requirements |
| <a id="a-02"></a>A-02 | Some GitHub features on private repositories depend on the plan, including push protection and some environment protection rules | Changes how secret scanning and approvals are provided ([D-07](decision-register.md#d-07-deployment-pipeline-and-environment-promotion), [D-15](decision-register.md#d-15-github-secrets-variables-and-leak-prevention)) | Check the account's plan when the first pipeline is set up |
| <a id="a-03"></a>A-03 | When a legacy application on another database is modernised, data migration is a separate piece of work rather than a template concern | Adds migration tooling to the template | Each application's target architecture |
