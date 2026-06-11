# ROSEA-Media Cost and Security Plan

## Summary

The current app is cheap to host because it is a static site, but it is not secure enough for shared AI usage.

The core issue is simple:

- GitHub Pages is fine for the UI
- GitHub Pages cannot securely hold secrets
- OpenAI and real auth require a backend

## Current state

Low-cost traits:

- one static page
- no server
- no database
- simple GitHub Pages deployment

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

## What GitHub Pages can and cannot do

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

## Baseline architecture recommendation

For the current system, the safest minimal direction is:

- keep the frontend static
- add one small backend for AI
- later move auth server-side
- later move source fetching server-side if needed

This keeps cost low while fixing the biggest security problem first.

## Conclusion

The current app is cheap but not secure enough for shared internal use. The smallest practical improvement is a split design: static frontend plus one backend endpoint for AI. After that, auth and source fetching can move behind the backend as needed.
