# Decision register

The decisions behind the [AI application standard](../README.md). The resulting design is in the [design](design.md). Settings that vary by owner or account are in [configuration-defaults.md](configuration-defaults.md) and cited here by ID (for example [C-02](configuration-defaults.md#c-02)).

Each entry gives the question, why it matters, the options considered and the decision. Status is **Decided** or **Open**. A decided entry is recorded as an ADR in [adrs/](../adrs/) with the same number (D-04 becomes ADR-0004), at which point the entry here shrinks to its title, status and a link. Decision numbers are never reused.

| ID | Decision | Outcome | Status |
|---|---|---|---|
| [D-01](#d-01-shape-of-the-outcomes) | Shape of the outcomes | Three repositories: the standard, the template, and the platform code released as versioned packages | Decided |
| [D-02](#d-02-backend-language-and-framework) | Backend language and framework | Python and FastAPI, with Ruff and Pyright | Decided |
| [D-03](#d-03-frontend-framework-and-hosting) | Frontend framework and hosting | Vite React single-page app on S3 and CloudFront, with the API as backend-for-frontend | Decided |
| [D-04](#d-04-identity-and-authorisation-model) | Identity and authorisation model | Provider-neutral OIDC with Entra ID as the default, app roles, no role hierarchy, local mock identity provider | Decided |
| [D-05](#d-05-network-placement) | Network placement | Network supplied as inputs, with an optional minimal network from the bootstrap | Decided |
| [D-06](#d-06-ingress-and-exposure-model) | Ingress and exposure model | CloudFront with AWS WAF and an internal load balancer as a VPC origin | Decided |
| [D-07](#d-07-deployment-pipeline-and-environment-promotion) | Deployment pipeline and environment promotion | One build deployed to every environment by image digest, with approvals on test and production | Decided |
| [D-08](#d-08-repository-home) | Repository home | Private repositories in a personal GitHub account | Decided |
| [D-09](#d-09-aws-bootstrap-resources) | AWS bootstrap resources | A bootstrap script per account, with a permissions boundary and S3 native state locking | Decided |
| [D-10](#d-10-directory-lookups) | Directory lookups | Through the API, only where an application needs them | Decided |
| [D-11](#d-11-local-development-environment) | Local development environment | WSL with Docker Engine | Decided |
| [D-12](#d-12-background-processing-pattern) | Background processing pattern | EventBridge Scheduler starting ECS tasks; an SQS worker when needed | Decided |
| [D-13](#d-13-secrets-management-and-rotation) | Secrets management and rotation | Secrets Manager; database credentials fetched with a short cache; RDS-managed rotation | Decided |
| [D-14](#d-14-developer-access-to-aws) | Developer access to AWS | IAM Identity Center single sign-on, with no access keys on laptops | Decided |
| [D-15](#d-15-github-secrets-variables-and-leak-prevention) | GitHub secrets, variables and leak prevention | Identifiers as variables, environment-scoped secrets only where unavoidable, secret scanning in CI and before each commit | Decided |
| [D-16](#d-16-observability) | Observability | CloudWatch with Container Insights and AWS's recommended alarms | Decided |
| [D-17](#d-17-reporting-and-data-supply) | Reporting and data supply | Operational screens in the application, defined exports for data supply, no generic data export | Decided |
| [D-18](#d-18-api-consumers-and-path-naming) | API consumers and path naming | Every interface is an API client; paths name the resource | Decided |
| [D-19](#d-19-api-exposure) | API exposure | Through each application's CloudFront and AWS WAF, built so a gateway can be added later | Decided |

---

### D-01 Shape of the outcomes

**Question.** Should the documented standard and the ready-to-use template be one artefact or two, and where should each live?

**Why it matters.** Each application copies the template and then owns its copy. Anything in it that should stay common (decisions, rationale, the development process) drifts in every copy. Separating them too far loses the in-repository specifications Claude needs to read.

| Option | Description | Assessment |
|---|---|---|
| A. One repository | Template tree plus `docs/standard/` | Simplest to start. Every derived repository inherits a copy of the standard that then drifts. |
| B. Two repositories | `ai-app-standard` (decisions, design, development process, setup guide) and `ai-app-template` (scaffold, generic specifications, workflows, `CLAUDE.md`). Generic specifications cite decision IDs, and the derived repository records the template version it came from. | One source of truth for the "why". The template stays lean. Needs discipline to keep specifications and decisions aligned. |
| C. Standard on a wiki or documentation site | The only repository is the template | One repository to maintain. Claude cannot read the wiki from the repository, so the template's specifications must carry everything Claude needs. |

**Decision.** B. The template holds specifications rather than code, so each application generates its own platform code (settings, logging, sign-in, the mock identity provider, Terraform and workflows). That platform code is built first inside the first application, in clearly separated modules, and extracted into a shared, versioned platform repository once a second application needs it.

**Update, 2026-11-12.** The platform repository is the third outcome, and was extracted sooner than planned (work plan decision 1, 2026-11-08): [`ai-app-platform`](https://github.com/supermanchris4/ai-app-platform) releases the API platform, the web platform and the mock identity provider as versioned packages, and the template's specifications say what each provides and what an application still writes (work plan F02.5). The Terraform modules and workflows, external users and the directory lookup follow (work plan F03.3 to F03.5). An application installs the packages at a version and keeps no copy, so a platform fix reaches every application as a new version instead of being made in each copy.

### D-02 Backend language and framework

**Question.** Should the API tier be Python and FastAPI, TypeScript and Express, or TypeScript on a structured framework? Which linter, formatter and type checker go with the chosen language?

**Why it matters.** Business logic belongs in the API, especially logic that is complex or must be applied consistently for every user and client. The backend language therefore carries most of an application's complexity. It also fixes the supporting tools: the schema migration tool (Alembic for Python, tools such as Prisma or Knex for TypeScript), the test framework, the Docker image and the CI steps. Supporting two backends in one template doubles the maintenance.

| Option | For | Against |
|---|---|---|
| A. Python, FastAPI | Layering, error taxonomy, audit, pagination and fixtures are well established in it. Python suits calculation-heavy, statistical and batch work. OpenAPI is generated from the code. | Two languages in one repository. Web types must be generated from OpenAPI rather than shared. |
| B. TypeScript, Express | One language end to end, with types shared directly | Express imposes no structure, so the layering would be kept by convention only |
| C. TypeScript, NestJS or Fastify | One language, with enforced structure and OpenAPI support | Rework of an existing Python scaffold with no functional gain |

**If the choice depends on the application.** The template carries one backend. An application that needs another language records an ADR and builds its API tier itself. The web tier, infrastructure, pipeline and specifications still come from the template, because none of them depend on the API language: the web tier talks only to the OpenAPI contract, and the pipeline builds whatever image `apps/api/Dockerfile` produces.

**Python development tools.** Code is written and maintained with Claude Code rather than in an editor, so the pipeline's checks are what the code is held to. Claude Code runs the same checks before it hands work over.

| Tool | Options | Choice |
|---|---|---|
| Linter and formatter | Ruff; Pylint with Black | Ruff. It formats as well as lints, and its `PL` rule set implements most of the Pylint rules. |
| Type checker | mypy; Pyright | Pyright in strict mode, installed as the `pyright[nodejs]` development dependency so it needs no separately installed Node. FastAPI, Pydantic 2 and SQLAlchemy 2's `Mapped` types are checked without plugins, and SQLAlchemy's mypy plugin is deprecated. Pyright is also the language server behind Claude Code's Python code intelligence plugin. |

**Decision.** A, with an OpenAPI-generated TypeScript client in the web tier, Ruff as linter and formatter, and Pyright in strict mode. One default backend, with a documented deviation path.

### D-03 Frontend framework and hosting

**Question.** What should an application's web interface be built with, and where should its sign-in tokens be held?

**Why it matters.** It determines whether there is a second container to run, patch and scale, which sign-in library is used and how exposed the user's tokens are.

A single-page app is a set of static files: all rendering happens in the browser and it talks only to the API. Next.js runs a Node server that renders pages and can call the API on the user's behalf, which suits public, search-indexed sites or pages that must be assembled server-side. Public, search-indexed pages are a separate site rather than part of an application, so an application's web interface sits behind sign-in.

RFC 10017, *OAuth 2.0 for Browser-Based Applications*, ranks three ways a browser application can handle sign-in, from most to least secure. In a **backend-for-frontend**, a server component signs the user in and holds the tokens, and the browser holds only a session cookie. In a **token-mediating backend**, a server signs the user in but gives the access token to the browser. In a **browser-based client**, the browser does everything and holds the tokens itself. The RFC recommends a backend-for-frontend for business applications and applications that handle personal data.

| Option | For | Against |
|---|---|---|
| A. Next.js on Fargate | Server rendering available | A second Fargate service with its own networking. Its common sign-in library (NextAuth v5) is still beta and in security-only maintenance. |
| B. Vite React single-page app on S3 and CloudFront, tokens in the browser | No web runtime to operate. The UI is just another API client. | Tokens are held in the browser, the pattern RFC 10017 ranks least secure |
| C. Vite React single-page app on S3 and CloudFront, with the API as backend-for-frontend | The same static files as B. The API signs users in as a confidential client and keeps the tokens; the browser holds only an HttpOnly, Secure, SameSite session cookie. CloudFront serves the app and `/api` on one address, so no extra container is needed. | The API holds sessions, in PostgreSQL so its containers stay interchangeable. Needs a client credential (preferably a certificate) in Secrets Manager and protection against cross-site request forgery on cookie-authenticated calls. Other systems still call with bearer tokens. |

**Evidence.** [RFC 10017: OAuth 2.0 for Browser-Based Applications](https://www.rfc-editor.org/rfc/rfc10017.txt); [Better Auth security update](https://better-auth.com/blog/security-update-july-2026). Check the exact wording of RFC 10017's recommendation before quoting it in the ADR.

**Moving an existing Next.js application to option C.**

| Part of a Next.js web tier | Under option C |
|---|---|
| UI components (button, badge, input), `cn` helper, `LocalDate`, `NavLink` | Kept unchanged. They are plain React. |
| Tailwind configuration and `globals.css` design tokens | Kept unchanged |
| `lib/api.ts` single API module and `ApiError` | Kept in pattern. Rewritten to send the session cookie and CSRF token rather than a fixed token and role header. |
| Hand-written API types | Replaced by types generated from the API's OpenAPI document ([D-02](#d-02-backend-language-and-framework)) |
| Authenticated layout role gate | Becomes a client-side route guard. It only decides what to show; the API still refuses anything the user's roles do not allow. |
| NextAuth configuration, middleware, login page and auth route | Replaced by sign-in through the API's `/api/auth/` endpoints. The identity provider provides the sign-in page. |
| Server components and server actions | Removed. Pages render in the browser after the JavaScript loads. |
| Routing by folder | Replaced by React Router |
| Web container, its secrets and its deployment | Replaced by an S3 bucket behind CloudFront, uploaded by the pipeline |

**Decision.** C, with a Tailwind and Radix UI kit, a single API module, a role-gated layout and no business rules in the UI.

### D-04 Identity and authorisation model

**Question.** How should internal users, external users and other systems authenticate and be authorised, and how does local development work without a real identity provider?

**Why it matters.** Every deployed environment needs real token validation, with simulated sign-in only on developers' machines, and an authorisation model to go with it.

| # | Fork | Options | Decision |
|---|---|---|---|
| a | Identity provider | A named provider (Entra ID, Cognito, Auth0); any OpenID Connect provider configured by discovery URL | Any OpenID Connect provider, configured by its discovery URL, with Entra ID as the documented default for internal users ([C-01](configuration-defaults.md#c-01)). The code holds no provider-specific logic beyond claim names. |
| b | Source of roles | App roles in the token's `roles` claim; groups; an application role table | App roles. They arrive in the token, need no extra lookup, and are assigned in the provider. |
| c | App registrations | One shared by web and API; separate web and API registrations; one confidential registration per issuer, plus one per consumer | One application registration per issuer, as a confidential web client that also exposes the API's scope and app roles. Each consumer system has its own registration. There is no separate web registration, because the browser does not sign in by itself. |
| d | Role taxonomy | A fixed set of roles; application-defined roles under a standard pattern | Application-defined roles named `{area}.{action}`, with human and machine roles separate and system roles prefixed `system.`. There is no role hierarchy: each route lists every role it allows. |
| e | Local development | Skip authentication when unset; a client-supplied role header; a local mock issuer that signs test tokens validated by the same code path | Local mock issuer. Production code then has no bypass branch. |
| f | External users | None; a second issuer validated alongside the internal one | Optional. An application with external users, such as customers or people from other companies, adds a second issuer (Entra External ID by default). |
| g | Calls a consumer makes for an external user | The user's own access token passed to the API; the consumer's own system token, with the user's identity asserted in the request | The user's own token for anything about that user, so the API itself enforces that they reach only their own records. The consumer's system token only for calls not about one user, such as reference data. |
| h | External user authorisation | Roles only; roles plus a record-level check | A record-level check in the service layer. Roles alone cannot set which records an external user can see, so each application defines that. |

**Decision.** As in the table.

### D-05 Network placement

**Question.** Should the application's Terraform create its own VPC, or deploy into a network supplied to it?

**Why it matters.** Where an account already has a network, an application that creates its own duplicates it and may not be permitted. Where an account has none, something must create one. The network is also the largest fixed cost: a NAT gateway, an internal load balancer and a small RDS instance together cost roughly USD 70 or more a month before any Fargate tasks.

| Option | Description | Assessment |
|---|---|---|
| A. Own VPC per application | Two Availability Zones, a NAT gateway, three subnet tiers | Self-contained. Each application pays for its own NAT gateway. |
| B. Supplied network | VPC ID and two subnet lists (`app_subnet_ids` for tasks and the database, `lb_subnet_ids` for the internal load balancer) passed in as required inputs | Works with any existing network. Fails the plan if a value is missing. |
| C. B, with an optional minimal network from the bootstrap | As B. For an account with no network, the bootstrap creates one VPC with private subnets in two Availability Zones and one NAT gateway, shared by the applications in that account. | Works in both kinds of account, and the application Terraform stays the same |

**Decision.** C.

### D-06 Ingress and exposure model

**Question.** How do people and other systems reach the application, and is it reachable from the internet?

**Why it matters.** The exposure model decides which edge services are used, how traffic to the API is encrypted and whether a web application firewall is needed. AWS recommends HTTPS between CloudFront and the load balancer. CloudFront VPC origins let the load balancer sit inside the network, reachable only through CloudFront, replacing the older approach of a public load balancer protected by a shared secret header.

| Option | Description | Assessment |
|---|---|---|
| A. Public ALB with ACM | ALB in public subnets, HTTPS, path routing | Simple. No edge caching for static files. |
| B. CloudFront to S3 and to a public ALB | Single origin, no CORS, default CloudFront certificate; the ALB accepts only requests carrying a shared secret header | No domain needed. CloudFront connects to the ALB over HTTP. |
| C. CloudFront to S3 and to an internal ALB as a VPC origin | As B, with AWS WAF on the distribution and an internal ALB as a CloudFront VPC origin over HTTPS. The ALB security group admits only the CloudFront managed prefix list. | Encrypted end to end, and the ALB is not reachable from the internet. Needs a certificate for the ALB, and so a domain. VPC origins do not support IPv6-only subnets. The VPC must have an internet gateway attached, which does not route traffic to the ALB. |
| D. Internal only | Internal ALB reachable over a private network or VPN | Smallest exposure. Excludes anyone not on that network. |

**Evidence.** [AWS: CloudFront with Application Load Balancers](https://aws.amazon.com/blogs/networking-and-content-delivery/optimizing-application-performance-the-strategic-benefits-of-combining-amazon-cloudfront-with-application-load-balancers); [AWS re:Post: CloudFront VPC origins](https://www.repost.aws/knowledge-center/cloudfront-vpc-origins); [AWS: CloudFront VPC origins developer guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html); [AWS: cross-account VPC origins](https://aws.amazon.com/about-aws/whats-new/2025/11/amazon-cloudfront-cross-account-vpc-origins).

**Decision.** C as the template default, with D documented for applications whose users are all on a private network.

### D-07 Deployment pipeline and environment promotion

**Question.** How do changes move from merge to production, who approves each step, and does Terraform or the pipeline own the running image version?

**Why it matters.** This is where the development process binds to the tooling.

| # | Fork | Options | Decision |
|---|---|---|---|
| a | Trigger | Manual dispatch; deploy `dev` on merge to `main` | Deploy `dev` on merge; test and production on approval |
| b | Promotion | Rebuild per environment; build once and deploy the same image digest to each environment | Build once and deploy the same build everywhere, with only configuration differing |
| c | Approvals | None; GitHub Environment required reviewers on test and production | Required reviewers and restricted deployment branches on each GitHub Environment ([C-14](configuration-defaults.md#c-14)) |
| d | Image version owner | A Terraform variable; the pipeline registers task definition revisions and Terraform ignores them | The pipeline, with immutable SHA tags |
| e | Infrastructure changes | Plan on PR, then apply on manual dispatch; plan on PR, apply only from `main` through an approved environment | The latter, with a read-only role for plans on pull requests |
| f | Database schema migrations | Manual; at API start-up; a one-off ECS task run by the pipeline before the service update | Pipeline-run one-off task |
| g | Third-party actions | Version tags; commit SHAs | Pinned to commit SHAs, with Dependabot proposing updates |
| h | Workflow token permissions | Set per workflow; none by default, granted per job | None by default, granted per job |

**Evidence.** Practitioner guidance on [OIDC and least-privilege tokens](https://dev.to/indra_gustiprasetya_a80a/github-actions-oidc-to-aws-10-tips-to-kill-static-keys-1fne) and [pinning actions](https://inventivehq.com/blog/github-actions-security-guide). Some environment protection rules on private repositories depend on the GitHub plan ([A-02](configuration-defaults.md#a-02)).

**Decision.** As in the table.

### D-08 Repository home

**Question.** Where are the standard, the template and the applications' repositories held?

**Why it matters.** The template must be usable from wherever application repositories are created. The pipelines' AWS trust uses the repository's immutable identifier, which includes its owner, so moving a repository to another owner breaks that trust until the roles are re-trusted.

**Decision.** Private repositories in a personal GitHub account, with `ai-app-template` marked as a template repository. A repository that later moves to another owner is re-trusted in AWS as part of the move.

### D-09 AWS bootstrap resources

**Question.** Should each repository create its own GitHub OIDC provider, Terraform deploy role and state bucket with a bootstrap script, or should they be provided per account?

**Why it matters.** An AWS account can hold only one GitHub OIDC provider, so accounts shared between applications need it created once. A role that can create infrastructure needs a permissions boundary. Terraform has deprecated the DynamoDB lock table in favour of native locking in the S3 state bucket.

**Evidence.** [HashiCorp: S3 backend](https://developer.hashicorp.com/terraform/language/backend/s3); [HashiCorp community: DynamoDB deprecation](https://discuss.hashicorp.com/t/deprecation-of-dynamodb-table-in-terraform-s3-backend/77060).

**Decision.** A bootstrap script per account that creates the OIDC provider (skipped if the account already has one), the Terraform deploy role with a permissions boundary, a read-only plan role and the state bucket with S3 native state locking (`use_lockfile`). It can also create the minimal network in [D-05](#d-05-network-placement). An operator runs it with their own single sign-on credentials rather than credentials passed into a workflow.

### D-10 Directory lookups

**Question.** Where an application needs to look people up in the identity provider's directory (for example Microsoft Graph for Entra ID), should the browser call the directory directly or should lookups go through the API?

**Why it matters.** Browser-side directory calls bypass the API-first rule and give every signed-in user a token that can read the whole directory.

**Decision.** Through the API, using an application permission scoped as narrowly as the directory allows, with results cached. The endpoint is optional, and an application that needs no lookups leaves it out.

### D-11 Local development environment

**Question.** How do developers run and test an application on their own machine: containers in WSL, tools installed directly on Windows, or a cloud-hosted development environment?

**Why it matters.** The answer decides what the template ships for local development: a Docker Compose file, a Windows setup guide or a cloud environment definition. It also decides how closely local testing matches the deployed Linux containers.

| Option | For | Against |
|---|---|---|
| A. WSL with Docker Engine inside it | No licence cost. Local containers match the deployed ones. | Needs WSL and administrator rights to install it |
| B. WSL with Docker Desktop | Simpler install and a graphical interface | Docker Desktop's licence requires a paid subscription for larger organisations |
| C. Tools installed directly on Windows, without containers | No WSL or Docker | Local PostgreSQL and language runtimes differ from the deployed Linux containers, and each setup drifts |
| D. Cloud-hosted development environment, such as GitHub Codespaces | Nothing to install locally. Every developer gets an identical environment. | Cost, and depends on network access to the service |

**Working practices for option A.** Source code is kept in the WSL Linux filesystem (for example `~/src`), not on the Windows drive under `/mnt/c`. Containers receive file-change events only for files stored in the Linux filesystem, and access is much faster there. WSL is kept up to date. Sources: [Docker: WSL best practices](https://docs.docker.com/desktop/features/wsl/best-practices/), [Docker blog: WSL 2 best practices](https://www.docker.com/blog/docker-desktop-wsl-2-best-practices/).

**Decision.** A. Native Linux or macOS with a Docker-compatible engine works the same way.

### D-12 Background processing pattern

**Question.** Should the template define a standard pattern for scheduled and asynchronous work?

**Why it matters.** Most line-of-business applications have some scheduled or batch work, such as data loads from other systems, exports, notification processing and raising alarms when a time threshold passes. Each application inventing its own pattern defeats the template.

| Option | Use |
|---|---|
| EventBridge Scheduler starting an ECS task from the API image with a different command | Scheduled batch and loads |
| SQS queue with a worker ECS service | Asynchronous work triggered by API actions |
| Step Functions | Multi-step processing with retries and compensation |

**Evidence.** AWS documents EventBridge Scheduler as the way to schedule ECS tasks. It is more scalable than EventBridge scheduled rules and runs cron schedules in any time zone ([AWS: scheduling ECS tasks with EventBridge Scheduler](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/tasks-scheduled-eventbridge-scheduler.html)).

**Decision.** EventBridge Scheduler scaffolded; the SQS worker and Step Functions documented for applications that need them.

### D-13 Secrets management and rotation

**Question.** How are application secrets stored, delivered to the API and rotated?

**Why it matters.** A secret injected into a container as an environment variable is read only at start-up, so after a rotation a running task keeps the old value until it is replaced by a new deployment. RDS can manage and rotate its master password itself. Terraform state holds any secret value Terraform creates.

| # | Fork | Options | Decision |
|---|---|---|---|
| a | Store | AWS Secrets Manager; Systems Manager Parameter Store | Secrets Manager, for its rotation support and RDS integration |
| b | Delivery of static secrets | Injected as environment variables at task start; fetched by the application | Injected at task start |
| c | Database credentials for the API | A password injected at task start, so rotation needs a redeployment; a password the API fetches from Secrets Manager at connection time with a short cache; IAM database authentication | Fetched at connection time with a short cache. IAM database authentication is assessed against its connection limits before it is adopted. |
| d | Rotation | Manual; scheduled rotation in Secrets Manager, with RDS-managed rotation for the master password | Scheduled rotation at the configured period ([C-15](configuration-defaults.md#c-15)) |
| e | Encryption keys | AWS-managed keys; customer-managed KMS keys | AWS-managed keys by default, with a customer-managed key as an input ([C-15](configuration-defaults.md#c-15)) |
| f | Secrets in Terraform | Generated by Terraform and stored in state; created by AWS services and only referenced by Terraform | Created by AWS services, such as RDS-managed passwords. No secret value is generated by Terraform or held in its state. |

**Evidence.** [AWS: Secrets management best practices](https://pages.awscloud.com/rs/112-TZM-766/images/SMWQ1D2S2%20Secrets%20management%20best%20practices.pdf); practitioner guidance on [ECS and Secrets Manager for database credentials](https://oneuptime.com/blog/post/2026-02-12-ecs-secrets-manager-database-credentials/markdown) and [Secrets Manager rotation](https://computingforgeeks.com/aws-secrets-manager-rotation-guide/).

**Decision.** As in the table.

### D-14 Developer access to AWS

**Question.** How do developers get AWS access from their machines, including from inside WSL, and what credentials do they hold locally?

**Why it matters.** Long-lived access keys on laptops are a common source of credential leaks. AWS recommends temporary credentials, such as those issued through IAM Identity Center. Local development needs no real application secret, so AWS access is the only credential a developer holds.

| Option | Description | Assessment |
|---|---|---|
| A. IAM Identity Center single sign-on | Developers sign in with `aws sso login` inside WSL and receive short-lived credentials, cached and refreshed by the AWS CLI | No stored keys. Browser sign-in from WSL may need the device-code option. |
| B. IAM user access keys held in a vault tool | Keys are kept in the operating system's secure store and exchanged for short-lived sessions | Long-lived keys still exist and must be rotated |
| C. No developer access to AWS | All AWS changes go through the pipelines | Simplest to control, but makes troubleshooting slow |

**Evidence.** [AWS: programmatic access with temporary credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/security-creds-programmatic-access.html); practitioner guidance on [AWS CLI single sign-on profiles](https://oneuptime.com/blog/post/2026-02-12-aws-cli-sso-profiles/markdown).

**Decision.** A, with developer access limited to development accounts or permission sets. Test and production change only through the pipelines.

### D-15 GitHub secrets, variables and leak prevention

**Question.** Which pipeline values are GitHub variables and which are secrets, at what scope, and how are secrets stopped from reaching GitHub?

**Why it matters.** With OIDC, the pipelines hold no AWS credentials. Most remaining values (role ARNs, region, VPC and subnet IDs, the Terraform backend settings) are identifiers rather than secrets. A secret pushed to a repository must be treated as exposed, even if it is deleted later.

| # | Fork | Options | Decision |
|---|---|---|---|
| a | Non-sensitive identifiers | GitHub secrets; GitHub variables | Variables |
| b | Scope | Repository; GitHub Environment | Environment scope for anything that differs by stage |
| c | Sensitive values a pipeline needs | GitHub secrets; read from Secrets Manager at run time through OIDC | Read from Secrets Manager at run time. Where a GitHub secret is unavoidable, it is environment-scoped behind required reviewers. |
| d | Leak prevention | GitHub push protection; a secret scan in the pull request checks; a pre-commit secret scan on developers' machines | All three where available. Push protection on private repositories depends on the GitHub plan ([A-02](configuration-defaults.md#a-02)), so the pull request checks run an open-source secret scanner as well. |

**Evidence.** [GitHub: secret scanning and push protection](https://docs.github.com/en/code-security/secret-scanning/working-with-secret-scanning-and-push-protection); practitioner guidance on [managing secrets in GitHub Actions](https://www.blacksmith.sh/blog/best-practices-for-managing-secrets-in-github-actions).

**Decision.** As in the table.

### D-16 Observability

**Question.** What monitoring should the template set up by default?

**Why it matters.** Without alarms, a failing service goes unnoticed until users report it.

**Evidence.** [AWS: ECS monitoring tools](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/monitoring-automated-manual.html); [AWS re:Post: ECS alarming best practices](https://repost.aws/articles/ARWM9LX0HDSXuzjK42wYr0PQ/ecs-incident-detection-and-response-alarming-best-practices); [cloudonaut: monitoring ECS and Fargate](https://cloudonaut.io/how-to-monitor-container-workloads-running-on-ecs-and-fargate/).

**Decision.** CloudWatch Logs with the configured retention ([C-09](configuration-defaults.md#c-09)), and:

- Container Insights enabled on the cluster. It is off by default and is needed for the running-task count.
- Alarms from AWS's recommended set for ECS: running tasks below the desired count, high CPU and memory, load balancer 5xx responses and target response time.
- Structured JSON logs.

If another monitoring platform is used, logs and alarms are forwarded to it from CloudWatch.

### D-17 Reporting and data supply

**Question.** Where is reporting built for an application, and how may data leave the application?

**Why it matters.** Data taken out of an application loses the controls the application applies to it: roles, record scope, audit and retention.

| Need | Where it is built |
|---|---|
| Operational screens | In the application |
| A defined supply of data to another organisation or system | A defined export or integration from the application, recorded each time it runs |
| Reporting in a separate BI tool | Only through an application ADR. The tool reads a published set of reporting views through a read-only database role, never the application's tables, audit records or personal information without the data owner's approval. |
| Generic data export | None. The template provides none, and an application does not add one. |

**Decision.** As in the table.

### D-18 API consumers and path naming

**Question.** How does an application serve more than one kind of client (its own web interface, other systems, a separate front end for external users) without duplicating logic? How are API paths named so that who calls is not confused with what the data is?

**Why it matters.** When each interface carries its own logic, behaviour drifts between them and replacing an interface means rebuilding it. "Customer" in a path could mean who is calling or what the data is, and staff work with customer data too.

| Option | Example | Assessment |
|---|---|---|
| i. A prefix naming the consumer | `/api/v1/customer/accounts/{id}` | Reads as customer data as easily as customer use. Duplicates operations shared by several consumers. |
| ii. Paths name the resource only | `/api/v1/customers/{id}` or `/api/v1/accounts/{id}`, with who may call each operation set by the caller's identity and roles | One operation serves every caller. The path says only what the data is. |
| iii. A channel-neutral prefix for operations published outside the application | `/api/v1/external/accounts/{id}` | Still encodes the consumer in the path, and duplicates shared operations |

**Decision.** Every interface is a client of the same API, and features are built as API operations first. Paths follow option ii. Any screens an application provides for external users are clients of the same operations other consumers call.

### D-19 API exposure

**Question.** How do systems outside the application's AWS account reach its API, and should an API management layer sit in front of it?

**Why it matters.** Without a common approach, each application exposes its API differently, and adding a gateway later means reworking each one.

| Option | Description | Assessment |
|---|---|---|
| A. Through each application's CloudFront and AWS WAF | Consumers call the API on the same address the web interface uses, with their own tokens | No new platform, and works from any host. Each application manages its own consumers and documentation. |
| B. An API gateway in front of application APIs | A gateway product publishes application APIs as products | One catalogue, and consistent onboarding, quotas and usage reporting. Needs a platform and an owner. |
| C. Private connectivity between a consumer's network and AWS | A network link for consumer calls | No internet path, but ties the application to where each consumer is hosted |

**Decision.** A, built so a gateway can be placed in front later without changing the application: the API validates every token itself rather than relying on a gateway, its contract is published as an OpenAPI document, and each consumer has its own app registration.
