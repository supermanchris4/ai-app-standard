# ADR-0003: Frontend framework and hosting

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Must

## Question

What should an application's web interface be built with, and where should its sign-in tokens be held?

## Why it matters

It determines whether there is a second container to run, patch and scale, which sign-in library is used and how exposed the user's tokens are.

A single-page app is a set of static files: all rendering happens in the browser and it talks only to the API. Next.js runs a Node server that renders pages and can call the API on the user's behalf, which suits public, search-indexed sites or pages that must be assembled server-side. Public, search-indexed pages are a separate site rather than part of an application, so an application's web interface sits behind sign-in.

RFC 10017, *OAuth 2.0 for Browser-Based Applications*, ranks three ways a browser application can handle sign-in, from most to least secure. In a **backend-for-frontend**, a server component signs the user in and holds the tokens, and the browser holds only a session cookie. In a **token-mediating backend**, a server signs the user in but gives the access token to the browser. In a **browser-based client**, the browser does everything and holds the tokens itself. The RFC recommends a backend-for-frontend for business applications and applications that handle personal data.

## Options

| Option | For | Against |
|---|---|---|
| A. Next.js on Fargate | Server rendering available | A second Fargate service with its own networking. Its common sign-in library (NextAuth v5) is still beta and in security-only maintenance. |
| B. Vite React single-page app on S3 and CloudFront, tokens in the browser | No web runtime to operate. The UI is just another API client. | Tokens are held in the browser, the pattern RFC 10017 ranks least secure |
| C. Vite React single-page app on S3 and CloudFront, with the API as backend-for-frontend | The same static files as B. The API signs users in as a confidential client and keeps the tokens; the browser holds only an HttpOnly, Secure, SameSite session cookie. CloudFront serves the app and `/api` on one address, so no extra container is needed. | The API holds sessions, in PostgreSQL so its containers stay interchangeable. Needs a client credential (preferably a certificate) in Secrets Manager and protection against cross-site request forgery on cookie-authenticated calls. Other systems still call with bearer tokens. |

## Evidence

[RFC 10017: OAuth 2.0 for Browser-Based Applications](https://www.rfc-editor.org/rfc/rfc10017.txt); [Better Auth security update](https://better-auth.com/blog/security-update-july-2026). Check the exact wording of RFC 10017's recommendation before quoting it in the ADR.

## Decision

C, with a Tailwind and Radix UI kit, a single API module, a role-gated layout and no business rules in the UI.

### Moving an existing Next.js application to option C

| Part of a Next.js web tier | Under option C |
|---|---|
| UI components (button, badge, input), `cn` helper, `LocalDate`, `NavLink` | Kept unchanged. They are plain React. |
| Tailwind configuration and `globals.css` design tokens | Kept unchanged |
| `lib/api.ts` single API module and `ApiError` | Kept in pattern. Rewritten to send the session cookie and CSRF token rather than a fixed token and role header. |
| Hand-written API types | Replaced by types generated from the API's OpenAPI document ([ADR-0002](0002-backend-language-and-framework.md)) |
| Authenticated layout role gate | Becomes a client-side route guard. It only decides what to show; the API still refuses anything the user's roles do not allow. |
| NextAuth configuration, middleware, login page and auth route | Replaced by sign-in through the API's `/api/auth/` endpoints. The identity provider provides the sign-in page. |
| Server components and server actions | Removed. Pages render in the browser after the JavaScript loads. |
| Routing by folder | Replaced by React Router |
| Web container, its secrets and its deployment | Replaced by an S3 bucket behind CloudFront, uploaded by the pipeline |
