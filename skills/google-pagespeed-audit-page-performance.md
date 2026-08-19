---
name: Audit a page with PageSpeed Insights
description: >-
  Run a Lighthouse audit on a URL through the Google PageSpeed Insights API and read the
  category scores, Core Web Vitals and top opportunities back correctly — including the
  soft failures that arrive inside an HTTP 200.
api: openapi/google-pagespeed-analysis-api-openapi.yml
operations:
  - runPagespeed
generated: '2026-08-13'
method: generated
source: >-
  Grounded in openapi/google-pagespeed-analysis-api-openapi.yml (operationId runPagespeed),
  openapi/_original/pagespeed-insights-discovery.json, conventions/, errors/ and
  authentication/ in this repo.
---

# Audit a page with PageSpeed Insights

One operation, `runPagespeed`. It is a GET, it is read-only, and it is slow.

## Before you call

- Get an API key at <https://console.cloud.google.com/apis/credentials> and pass it on the
  `key` query parameter. The docs call the key optional. It is not: an unkeyed call is charged
  to a shared anonymous pool whose quota is exhausted, and returns 429 immediately.
- Set your client timeout to **at least 120 seconds**. The call runs a real Lighthouse audit
  synchronously; 120s is the documented maximum. A 30-second default client timeout is the
  single most common integration failure here.
- Analysis is not free of variance. Two runs on the same URL will not return identical scores.
  Do not diff raw scores between runs and report the delta as a regression.

## Call it

`runPagespeed` — `GET /runPagespeed` on `https://www.googleapis.com/pagespeedonline/v5`

Query parameters, all from the spec:

| Parameter | Required | Notes |
|---|---|---|
| `url` | yes | Absolute http(s) URL to analyse |
| `strategy` | no | `MOBILE` (default) or `DESKTOP` — run both if you want a full picture |
| `category` | no | Repeatable: `PERFORMANCE`, `ACCESSIBILITY`, `BEST_PRACTICES`, `SEO`. Defaults to performance only |
| `locale` | no | e.g. `en_US`; changes the language of every audit title and description |
| `utm_campaign`, `utm_source` | no | Analytics attribution |
| `captchaToken` | no | Only when a previous response demanded one |

Ask for exactly the categories you will read. Each extra category adds real audit time.

## Trim the response before you parse it

A full PageSpeed response is very large. Use the `fields` partial-response selector:

```
&fields=id,analysisUTCTimestamp,lighthouseResult/categories/*/score,lighthouseResult/audits/largest-contentful-paint
```

This is the highest-leverage thing an agent can do against this API — it cuts the payload by
orders of magnitude and removes most of the parsing risk.

## Read the result in this order

1. **`captchaResult`** — if it is not the not-needed value, the analysis did not happen.
   Re-issue with `captchaToken`. Do not read scores off this response.
2. **`lighthouseResult.runtimeError`** — present means the audit failed hard enough that the
   result must be discarded, even though the HTTP status was 200. Check `.code` and stop.
3. **`lighthouseResult.categories.<name>.score`** — a 0.0–1.0 float, not a 0–100 integer.
   Multiply by 100 only at the point of display.
4. **`lighthouseResult.audits.<id>`** — individual audits. Use
   `lighthouseResult.categories.performance.auditRefs[].weight` to rank which ones actually
   move the score before you recommend anything.
5. **`loadingExperience` / `originLoadingExperience`** — real-user Chrome UX Report field data.
   **Google has announced it plans to remove these from this API.** Treat them as optional and
   read field data from the CrUX API instead if you depend on it.

Lab data (`lighthouseResult`) and field data (`loadingExperience`) disagree routinely and are
not errors when they do — one is a synthetic run, the other is aggregated real traffic.

## Handle failure

Errors use the Google API envelope, not RFC 9457. Branch on
`error.details[].reason`, not on the human-readable message:

- **400 / `API_KEY_INVALID`** — key missing, malformed, or restricted away from
  `pagespeedonline.googleapis.com`. Fix the key; retrying will not help.
- **400 / `INVALID_ARGUMENT`** — bad `url`, `strategy` or `category` value.
- **429 / `RATE_LIMIT_EXCEEDED`** — quota exhausted. **No `Retry-After` and no `RateLimit-*`
  header is returned**, so back off exponentially with jitter, blind. If you are unkeyed, this
  is why; add a key.
- **500 / `INTERNAL`** — Lighthouse failed server-side. Retry; the call is a safe GET, so retries
  have no side effects and need no idempotency key.

## What this API does not have

No writes. No pagination. No webhooks or events. No idempotency-key contract. No sandbox or
test mode — every call analyses a live URL. No rate-limit response headers. Do not write code
that looks for any of them.
