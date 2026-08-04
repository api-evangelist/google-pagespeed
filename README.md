# Google PageSpeed (google-pagespeed)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Google PageSpeed Insights provides APIs for analyzing the performance of web pages on both mobile and desktop devices, returning performance scores, Core Web Vitals metrics, and actionable optimization recommendations powered by Lighthouse.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/google-pagespeed/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/google-pagespeed/refs/heads/main/apis.yml)

## Scope

- **Type:** Index

## Tags

- Core Web Vitals
- Google
- Lighthouse
- Page Speed
- SEO
- Web Performance

## Timestamps

- **Created:** 2026-03-13
- **Modified:** 2026-05-19

## APIs

### PageSpeed Insights API

The PageSpeed Insights API analyzes the content of a web page and generates suggestions to make it faster. It runs Lighthouse audits on the given URL and returns performance scores, Core Web Vitals metrics (Largest Contentful Paint, First Input Delay, Cumulative Layout Shift), field data from the Chrome User Experience Report, and detailed lab data with optimization opportunities and diagnostics.

- **Human URL:** [https://developers.google.com/speed/docs/insights/v5/get-started](https://developers.google.com/speed/docs/insights/v5/get-started)
- **Base URL:** `https://www.googleapis.com/pagespeedonline/v5`

#### Tags

- Audits
- Core Web Vitals
- Lighthouse
- Performance

#### Properties

- [Documentation](https://developers.google.com/speed/docs/insights/v5/reference)
- [OpenAPI](openapi/pagespeed-insights-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/pagespeed-insights.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/pagespeed-insights.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Authentication](https://developers.google.com/speed/docs/insights/v5/get-started#APIKey)
- [Getting Started](https://developers.google.com/speed/docs/insights/v5/get-started)
- [JSON Schema](json-schema/google-pagespeed-result-schema.json) — [JSON Schema](https://json-schema.org/specification)

## Common Properties

- [GitHub Organization](https://github.com/pagespeed)
- [Portal](https://pagespeed.web.dev/)
- [Getting Started](https://developers.google.com/speed/docs/insights/v5/get-started)
- [Documentation](https://developers.google.com/speed/docs/insights)
- [Authentication](https://developers.google.com/speed/docs/insights/v5/get-started#APIKey)
- [Terms of Service](https://developers.google.com/terms)
- [Privacy Policy](https://policies.google.com/privacy)
- [Status Page](https://status.cloud.google.com/)
- [Support](https://support.google.com/webmasters)
- [Console](https://console.cloud.google.com/apis/library/pagespeedonline.googleapis.com)
- [JSON-LD](json-ld/google-pagespeed-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
