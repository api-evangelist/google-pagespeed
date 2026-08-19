---
name: Track Core Web Vitals across a set of pages
description: >-
  Use the PageSpeed Insights API to build and maintain a Core Web Vitals scorecard over many
  URLs and both form factors, within an undocumented quota and with no rate-limit signal.
api: openapi/google-pagespeed-analysis-api-openapi.yml
operations:
  - runPagespeed
generated: '2026-08-13'
method: generated
source: >-
  Grounded in openapi/google-pagespeed-analysis-api-openapi.yml (operationId runPagespeed),
  rate-limits/google-pagespeed-rate-limits.yml, conventions/ and lifecycle/ in this repo.
---

# Track Core Web Vitals across a set of pages

The API has one operation, so a scorecard is a fan-out of `runPagespeed` calls. Everything hard
about this skill is scheduling, not calling.

## Shape of the job

For each URL in your set, for each `strategy` you care about (`MOBILE` and `DESKTOP` are
separate audits and separate quota units), call `runPagespeed` once. N URLs × 2 strategies = 2N
calls, each taking seconds to a couple of minutes.

## Budget before you fan out

- Google publishes **no numeric quota** for this API. The limit exists — the 429 body names it
  as `defaultPerDayPerProject` on metric `pagespeedonline.googleapis.com/default` with unit
  `1/d/{project}` — but the value is only visible on the Cloud Console quotas page for your own
  project. Look it up there before sizing the job; do not trust a number you read in a blog post.
- There are **no rate-limit response headers**. You cannot watch a remaining-budget counter.
  Meter yourself: a fixed concurrency (small — single digits) plus exponential backoff with
  jitter on every 429.
- Use `quotaUser` if one key serves several tenants, so a single noisy tenant does not consume
  everyone's daily allowance.

## Per call

Request only what the scorecard displays:

```
GET /runPagespeed?url=<page>&strategy=MOBILE&category=PERFORMANCE
    &fields=id,analysisUTCTimestamp,loadingExperience/metrics,lighthouseResult/categories/performance/score,lighthouseResult/audits/largest-contentful-paint,lighthouseResult/audits/cumulative-layout-shift
```

Timeout ≥ 120 seconds per call. Persist `analysisUTCTimestamp` with every row — it is the only
thing that makes two runs comparable.

## Which numbers go in the scorecard

Field data (real users, from the Chrome UX Report) and lab data (this synthetic run) are
different measurements and belong in different columns:

- **Field** — `loadingExperience.metrics.LARGEST_CONTENTFUL_PAINT_MS`,
  `CUMULATIVE_LAYOUT_SHIFT_SCORE`, `INTERACTION_TO_NEXT_PAINT`,
  `EXPERIMENTAL_TIME_TO_FIRST_BYTE`. Each carries a `percentile` and a `distributions[]` of
  min/max/proportion buckets plus a `category` of FAST / AVERAGE / SLOW. Report the percentile
  and the bucket, not a single made-up grade.
- **Lab** — `lighthouseResult.categories.performance.score` (0.0–1.0) and the individual audits.
- `originLoadingExperience` is the whole origin, not this page. Never mix it into a per-page row.

**Migration warning:** Google has stated it intends to stop returning Chrome UX Report field
data from this API and points callers at the CrUX API / CrUX History API instead. No date has
been announced. If your scorecard's field columns matter, source them from the CrUX API now and
use PageSpeed only for lab data. See `lifecycle/google-pagespeed-lifecycle.yml`.

## Reliability rules

- Scores move run to run without anything changing on the page. Compare rolling medians across
  several runs, never two single runs.
- A 200 is not a success: check `captchaResult`, then `lighthouseResult.runtimeError`, before
  writing any row. A page that failed to load yields a plausible-looking low score that is
  actually garbage.
- Retries are free — the operation is a GET with no side effects and no idempotency key needed.
- Lighthouse major versions change scoring. Store `lighthouseResult.lighthouseVersion` on every
  row so a step change in your trend line can be explained instead of investigated.
