# ROSEA-Media Cost and Security Plan

## Summary

The current app is cheap to host because it is a static site, but it is not secure enough for shared AI usage.

The core issue is simple:

- static hosting is fine for the UI
- static hosting cannot securely hold secrets
- OpenAI and real auth require a backend

## Current state

Low-cost traits:

- one static page
- no server
- no database
- simple static deployment

Current security weaknesses:

- hardcoded login credentials in client code
- session state stored in `sessionStorage`
- OpenAI called directly from the browser
- public content fetched through third-party proxies

## Cost picture

Main cost drivers are not hosting. They are:

- OpenAI usage
- analyst time lost to slow scans
- proxy instability
- future maintenance of the monolithic file

Static hosting cost is near zero. Usage and reliability are the real cost concerns.

## What static hosting can and cannot do

Good fit:

- static UI
- filters
- exports
- public documentation

Not sufficient for:

- private API keys
- real authentication
- rate limiting
- server-side logging
- controlled proxying

## Secure AI pattern

Required pattern:

1. Browser completes the scan.
2. Browser sends compact article data to a backend endpoint.
3. Backend validates the request.
4. Backend calls OpenAI using a server-side secret.
5. Backend returns only the summary.

This is the minimum change needed to remove the key from the browser.

## Cost controls

Immediate controls:

- keep the UI static
- do not auto-run AI by default
- cap article count
- cap article summary length
- deduplicate before sending to AI
- make broad scans opt-in rather than default

Backend controls:

- per-user rate limits
- daily quotas if needed
- request logging
- timeout limits

Pipeline controls:

- bounded retries for unstable sources
- short-lived cache for repeated fetches during a scan
- append-only batch handling so successful results are preserved
- degraded-source monitoring so failures are visible without stopping the workflow
- source-level timeout and parse error reporting

## Baseline architecture recommendation

For the current system, the safest minimal direction is:

- keep the frontend static
- add one small backend for AI
- later move auth server-side
- formalize the browser-side scan pipeline so partial failures degrade safely
- later move source fetching server-side if needed

This keeps cost low while fixing the biggest security problem first and improves reliability without forcing an early platform rebuild.

## Conclusion

The current app is cheap but not secure enough for shared internal use. The smallest practical improvement is a split design: static frontend plus a minimal backend for auth and AI, with a more explicit scan pipeline on the frontend so broken sources do not take down the workflow. After that, source fetching can move behind the backend only if reliability or policy justifies it.
