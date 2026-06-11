# ROSEA-Media V1 Cost Addendum

## Purpose

This document adds a future-state cost view for:

- [v1-architecture-spec.md](</c:/Users/Hussain/Fawad-Software-Projects/ROSEA-Media/docs/v1-architecture-spec.md>)

It does not replace the baseline current-state view in:

- [cost-security-plan.md](</c:/Users/Hussain/Fawad-Software-Projects/ROSEA-Media/docs/cost-security-plan.md>)

## Fixed assumptions

- `Cloudflare Pages + Workers`
- `TypeScript` without `React`
- one server-side username/password login
- OpenAI key stored only in server-side secrets

## Main cost buckets

- static hosting
- serverless backend execution
- OpenAI usage

If source fetching stays in the browser during V1, there is no new backend cost for feed collection.

## Chosen cost path

`Cloudflare Pages + Workers` is the chosen V1 path because it keeps the stack simple and should remain free or near-free for a small internal tool.

Expected posture:

- static frontend can stay free
- backend can start on Workers Free
- the main variable cost is OpenAI, not hosting

Secrets note:

- `GitHub Secrets` are useful for CI and deployment automation
- runtime secrets should still live in Cloudflare, not in the static app build

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

Mitigation:

- rate-limit AI
- keep AI opt-in
- keep source fetching client-side in early V1

## Recommended V1 cost posture

1. keep the frontend static
2. avoid adding a database
3. add only the login and AI backend endpoints
4. keep payloads small
5. use Cloudflare free infrastructure as long as usage allows

## Upgrade triggers

Move beyond the free-first setup only when:

- AI usage exceeds free execution limits
- stronger auth requirements appear
- reliability needs justify server-side source aggregation
- user volume grows beyond a small analyst group

## Platform notes as of June 11, 2026

- Cloudflare Workers pricing: https://developers.cloudflare.com/workers/platform/pricing/
- Cloudflare Pages limits: https://developers.cloudflare.com/pages/platform/limits/

These should be rechecked before deployment because limits and pricing can change.

## Recommendation

The cheapest secure V1 is a narrow change, not a rebuild: static frontend, small backend, server-side secrets, and simple auth. That keeps cost close to zero while removing the largest security problems.
