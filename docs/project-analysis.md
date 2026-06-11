# ROSEA-Media Project Analysis

## Summary

`ROSEA-Media` is a static, browser-only humanitarian monitoring tool for OCHA ROSEA workflows. It scans public sources across 25 countries, classifies items by country, theme, and severity, and lets analysts export or summarize the results.

The current implementation is useful as a lightweight working build, but it is not a secure internal system. Login is client-side only, OpenAI is called from the browser, and source fetching depends on third-party CORS proxies.

## What it does

Main workflow:

1. User signs in through a browser-side login form.
2. User selects countries, themes, timeframe, source, and scan mode.
3. The app fetches content from public sources.
4. Results are normalized into alert objects.
5. Alerts are classified, ranked, filtered, and displayed.
6. The user exports results as `.txt`, `.csv`, or `.doc`.
7. Optionally, the user sends the current result set to OpenAI for a sitrep draft.

This is a monitoring and briefing tool, not a database-backed application.

## Current architecture

- `index.html` contains the full app: HTML, CSS, and JavaScript.
- `.github/workflows/static.yml` deploys the site through the current static hosting pipeline.
- There is no backend, database, build step, or test suite.

Runtime shape:

- static delivery through the current hosting setup
- client-side auth using a hardcoded `USERS` array
- in-memory alert list in `allAlerts`
- browser-side exports
- browser-side OpenAI call
- ad hoc browser-side source pipeline with proxy racing and limited failure visibility

## Data sources

Live sources:

- ReliefWeb API
- GDACS RSS
- UN News Africa RSS
- WHO Disease Outbreak News RSS
- FEWS NET RSS
- AllAfrica headlines feed
- Google News RSS search results

Local hardcoded data:

- countries
- regions
- themes
- keyword dictionaries
- sweep keywords
- country media domains
- login users

## Key implementation details

- The app uses a shared alert shape with fields like `title`, `country`, `theme`, `severity`, `summary`, `date`, `source`, `url`, and `dataSource`.
- Classification is heuristic through functions such as `matchCountry(...)`, `detectTheme(...)`, and `scoreSeverity(...)`.
- Duplicate reduction happens after cross-source aggregation.
- Google News supports `quick`, `standard`, and `full` scan modes.
- Source collection is effectively a lightweight pipeline, but retry, cache, append, and monitoring behavior are not yet formalized as modules.

## Main risks

Security:

- login credentials are exposed in client code
- login is not a real security boundary
- OpenAI key is used in the browser
- feed content passes through third-party proxies

Reliability:

- source formats may change
- proxy availability is outside project control
- Google News results are variable
- partial source failures are not surfaced clearly enough for operators
- scan reliability depends on inline logic rather than a defined pipeline layer

Maintainability:

- all logic lives in one file
- UI, fetching, auth, exports, and AI are tightly coupled
- there are no tests or module boundaries

## Assessment

This is a credible internal working build with low hosting cost and clear analyst value. It is not yet suitable as a secure shared application.

The practical next steps are:

1. move OpenAI behind a backend
2. replace browser-only auth
3. move stable config out of inline JavaScript
4. split the monolithic file into modules
5. formalize the scan pipeline with retry, cache, append, and degraded-source monitoring

Until then, it should be treated as a convenience dashboard rather than a trusted internal platform.
