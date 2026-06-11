# ROSEA-Media V1 Architecture Spec

## Purpose

This document defines the chosen V1 design. The baseline current-state documents are:

- [project-analysis.md](</c:/Users/Hussain/Fawad-Software-Projects/ROSEA-Media/docs/project-analysis.md>)
- [cost-security-plan.md](</c:/Users/Hussain/Fawad-Software-Projects/ROSEA-Media/docs/cost-security-plan.md>)

## Fixed V1 decisions

- frontend stack: `TypeScript` without `React`
- hosting: `Cloudflare Pages + Workers`
- auth: one simple server-side username/password login
- secrets: only server-side, never in the browser
- config: move stable reference data into JSON files

## V1 goals

- keep the product simple
- remove browser-exposed secrets
- remove hardcoded client auth
- preserve the current analyst workflow
- avoid adding a database in V1

## Chosen architecture

Frontend:

- static site on `Cloudflare Pages`
- TypeScript modules
- local rendering, filtering, and exports
- browser-side source fetching can remain in V1

Backend:

- `Cloudflare Workers` or `Pages Functions`
- `POST /api/login`
- `POST /api/logout`
- optional `GET /api/session`
- `POST /api/ai-summary`

## Why this path

- small refactor instead of full rewrite
- secure secret storage
- simple shared deployment
- low-cost free-first hosting

Vercel is technically capable, but not chosen because `Vercel Hobby` is documented for personal, non-commercial use and this project is meant to become a shared internal tool.

## Setup and hosting

Official docs:

- Cloudflare Pages getting started: https://developers.cloudflare.com/pages/get-started/
- Cloudflare Pages Git integration: https://developers.cloudflare.com/pages/get-started/git-integration/
- Cloudflare Pages Functions get started: https://developers.cloudflare.com/pages/functions/get-started/
- Cloudflare Workers CLI guide: https://developers.cloudflare.com/workers/get-started/guide/

Setup flow:

1. Split the app into static frontend files and backend function files.
2. Connect the GitHub repo to Cloudflare Pages.
3. Publish the static frontend through Pages.
4. Add backend endpoints through Pages Functions or Workers.
5. Store secrets in Cloudflare, not in the repo or browser.
6. Deploy from Git pushes.

Required server-side secrets:

- `OPENAI_API_KEY`
- `AUTH_USERNAME`
- `AUTH_PASSWORD_HASH`
- `SESSION_SECRET`

GitHub Secrets note:

- use `GitHub Secrets` for deployment and CI only
- do not rely on `GitHub Secrets` as runtime app secrets for a static frontend
- runtime secrets for login and OpenAI must live in Cloudflare server-side secrets

## Proposed structure

```text
/
  index.html
  src/
    app.ts
    auth.ts
    scan.ts
    sources.ts
    classify.ts
    export.ts
    ai.ts
    ui.ts
    config.ts
  config/
    countries.json
    themes.json
    sources.json
    scan-settings.json
  functions/
    login.ts
    logout.ts
    session.ts
    ai-summary.ts
```

## Auth design

Rules:

- no hardcoded users in the frontend
- no plaintext password in the repo
- use one server-side username
- use one server-side password hash
- use a signed HTTP-only session cookie

Login flow:

1. User posts username/password to `POST /api/login`.
2. Backend checks `AUTH_USERNAME`.
3. Backend verifies `AUTH_PASSWORD_HASH`.
4. Backend sets session cookie.
5. Protected endpoints require that cookie.
6. `POST /api/logout` clears it.

## AI design

`POST /api/ai-summary` should:

- require a valid session
- accept normalized article payloads
- cap article count
- cap summary length
- call OpenAI using `OPENAI_API_KEY`
- return only the generated report

V1 policy:

- max `50` articles
- max `220` summary characters per article
- no auto-analysis by default

## JSON config

Move these out of inline JavaScript:

- `countries.json`
- `themes.json`
- `sources.json`
- `scan-settings.json`

This keeps logic cleaner and makes updates safer.

## What stays the same in V1

- country and theme filters
- scan modes
- source tabs
- exports
- AI analysis panel

## What changes in V1

- remove browser API key input
- replace hardcoded `USERS`
- route AI through the backend
- move stable config into JSON
- split the single-file app into TypeScript modules

## Acceptance criteria

V1 is complete when:

1. The browser never sends an OpenAI key.
2. Hardcoded `USERS` are removed.
3. Login uses one server-side username and password hash.
4. AI works through a backend endpoint.
5. Static config is loaded from JSON.
6. The current scan and export workflow still works.

## Phases

Phase 1:

- convert JS to TypeScript modules
- move config into JSON
- add login, logout, and AI endpoints
- move secrets to Cloudflare

Phase 2:

- enforce session checks
- remove client-side login truth
- add frontend session handling

Phase 3:

- optionally move source fetching server-side
- add caching and observability

## Final recommendation

Build V1 as a small static-plus-serverless Cloudflare app. Keep the frontend simple, keep auth simple, keep secrets server-side, and avoid a database until it is clearly needed.
