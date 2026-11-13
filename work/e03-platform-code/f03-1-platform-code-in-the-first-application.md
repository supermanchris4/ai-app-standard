# F03.1 Platform code in the first application

Part of [E03 Platform code](README.md) in the [work plan](../README.md). An enabler Feature: it builds the platform code inside the first application, in modules kept separate from its domain code, as the first application is rearchitected to the template.

The Stories here say what the platform code must provide. The first application's own delivery plan holds the work, and its tasks are tracked there.

## Benefits

- The first application runs on code that meets the template's security, audit and logging requirements.
- The modules are ready to extract into packages without being rewritten.

## Acceptance criteria

1. Each module meets the template specification it implements, shown by the first application's tests.
2. No platform module imports the first application's domain code.

## Dependencies

- The first application's rearchitecture.

## Modules

The specifications named are in the template's `specs/` folder.

| Module | Contents | Template specification | Later released as |
|---|---|---|---|
| API platform | Settings base and deployed-environment checks, JSON logging, correlation and request logging middleware, the shared error format, the health endpoint, database access with the Secrets Manager credential provider, the audit service | `20-api-scaffold.md`, `31-audit.md` | uv git dependency with a tag and subdirectory |
| Sign-in and access | Issuers from discovery, bearer token validation, web sign-in as backend-for-frontend, sessions, cross-site request forgery checks, `AuthContext`, actor types, `require_role` and the record-scope check | `50-auth.md`, `20-api-scaffold.md` | Part of the API platform package |
| Local mock identity provider | The two-issuer OpenID Connect provider for developers' machines | `11-local-development.md` | Container image |
| Web platform | The API module wrapper, session handling and route guard, environment banner, `LocalDate` and `NavLink`. The component kit stays in each application's `src/components/ui/`, as the template's `40-ui.md` places it (the first application's delivery plan) | `40-ui.md` | Scoped npm package |

## Stories

### S03.1.1 Build the API platform module

As an application developer, I want the API platform as a separate module, so that my API meets the template's settings, logging, error, health, database and audit rules without writing them.

**Builds on:** [D-02](../../docs/decision-register.md#d-02-backend-language-and-framework), [D-13](../../docs/decision-register.md#d-13-secrets-management-and-rotation).

**Acceptance criteria:** tests cover each acceptance criterion in `20-api-scaffold.md` that does not depend on application wiring.

**Delivered by:** The first application's delivery plan.

### S03.1.2 Build the sign-in and access module

As an application developer, I want sign-in and access control as a separate module, so that every application signs people in and checks roles the same way.

**Builds on:** [D-03](../../docs/decision-register.md#d-03-frontend-framework-and-hosting), [D-04](../../docs/decision-register.md#d-04-identity-and-authorisation-model).

**Acceptance criteria:** tokens signed by any key other than the issuer's are rejected; the session cookie is `HttpOnly`, `Secure` and `SameSite=Lax`; a state-changing request without the cross-site request forgery token returns 403.

**Delivered by:** The first application's delivery plan.

### S03.1.3 Build the local mock identity provider

As a developer, I want a local identity provider standing in for each issuer, so that I can sign in locally through the same code as in AWS.

**Builds on:** [D-04](../../docs/decision-register.md#d-04-identity-and-authorisation-model), [D-11](../../docs/decision-register.md#d-11-local-development-environment).

**Acceptance criteria:** as in the local mock identity provider section of `11-local-development.md`.

**Delivered by:** The first application's delivery plan.

### S03.1.4 Build the web platform module

As an application developer, I want the web shell as a separate module, so that my pages sign in, call the API and show dates the same way as every other application.

**Builds on:** [D-03](../../docs/decision-register.md#d-03-frontend-framework-and-hosting).

**Acceptance criteria:** its component tests and accessibility checks pass; it holds no business rules and no token handling.

**Delivered by:** The first application's delivery plan.
