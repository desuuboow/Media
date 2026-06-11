# ROSEA-Media V1 Cost Addendum

## Purpose

This document adds a future-state cost view for:

- [v1-architecture-spec.md](</c:/Users/Hussain/Fawad-Software-Projects/ROSEA-Media/docs/v1-architecture-spec.md>)

It does not replace the baseline current-state view in:

- [cost-security-plan.md](</c:/Users/Hussain/Fawad-Software-Projects/ROSEA-Media/docs/cost-security-plan.md>)

## Fixed assumptions

- `Vercel` is used for build and test deployments
- the codebase is `TypeScript` without `React`
- one server-side username/password login
- OpenAI key stored only in server-side secrets
- stable reference data lives in external config files

## Main cost buckets

- preview and test hosting
- serverless backend execution
- OpenAI usage
- migration effort to organization-approved hosting

If source fetching stays in the browser during V1, there is no new backend cost for feed collection.

The V1 cost model assumes the scan pipeline remains lightweight but gains:

- bounded retries
- short-lived cache
- append-only batch handling
- degraded-source monitoring

## Chosen cost path

The chosen V1 path is to keep the initial environment small and disposable:

- use Vercel for preview and test deployments
- keep the frontend static
- keep backend endpoints minimal
- store secrets server-side only
- move to organization-approved hosting after validation

Expected posture:

- preview hosting should stay low cost
- backend execution should stay small
- the main variable cost is OpenAI, not hosting
- pipeline reliability logic should add implementation effort more than hosting cost

Secrets note:

- deployment settings can live in the hosting platform
- runtime secrets should never live in browser code
- runtime secrets should stay server-side

## OpenAI cost controls

V1 should keep AI usage tight:

- max `50` alerts per request
- max `220` summary characters per alert
- AI runs only on explicit user action
- auto-analysis stays off by default
- duplicates removed before sending

## Cost risks

1. AI overuse
2. scope creep from moving all source fetching server-side too early
3. spending time on host-specific changes before the workflow is stable

Mitigation:

- rate-limit AI
- keep AI opt-in
- keep source fetching client-side in early V1
- separate app code from host-specific deployment details
- keep pipeline monitoring lightweight and tied to the scan workflow rather than adding a full observability stack in V1

## Recommended V1 cost posture

1. keep the frontend static
2. avoid adding a database
3. add only the login and AI backend endpoints
4. keep payloads small
5. keep deployment settings out of application code
6. use the test host only until the workflow is validated
7. keep pipeline reliability features in code-level modules, not paid infrastructure

## Upgrade triggers

Move beyond the Vercel test setup when:

- the workflow has been validated with real users
- the organization-approved host is ready
- stronger auth requirements appear
- reliability needs justify server-side source aggregation
- user volume grows beyond a small analyst group
- pipeline monitoring needs exceed what a lightweight frontend-plus-serverless model can support

## Recommendation

The cheapest secure V1 is a narrow change, not a rebuild: static frontend, small backend, server-side secrets, and simple auth. Build and test on Vercel, then move the same codebase to organization-approved hosting after validation.
