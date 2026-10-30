# Northbeam

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
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Northbeam is a multi-touch marketing attribution platform for e-commerce brands. It joins first-party click and view data collected by its own browser pixel to order-level revenue and to ad spend across every channel, then reports channel, campaign, adset and ad level ROAS, CAC, AOV and creative performance under a choice of attribution models and windows. The developer surface is three REST APIs published as OpenAPI: an Orders API for pushing purchase data in as the revenue ground truth, a Spend API for uploading cost from channels Northbeam does not integrate with natively, and an asynchronous Data Export API for pulling attribution metrics back out to Northbeam documents, GCS or S3. Northbeam also runs a first-party remote MCP server at mcp.northbeam.io, OAuth-secured and read-only, which it documents as a custom connector for Claude and ChatGPT.

**Areas:** Marketing Attribution · Multi-Touch Attribution · E-Commerce · ROAS · Media Mix Modeling · Creative Analytics · Performance Marketing · Advertising · Marketing Analytics · Agents · Attribution

## Northbeam on APIs.io

Everything profiled for Northbeam is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/northbeam/](https://apis.io/providers/northbeam/)** — or go straight to one of the APIs or properties below.

### APIs (8)

- [Northbeam MCP Server](https://apis.io/apis/northbeam/mcp/) — First-party remote Model Context Protocol server giving an agent read-only access to the caller's Northbeam dashboards — performance, attribution, spend and…
- [Northbeam Attribution Models API](https://apis.io/apis/northbeam/northbeam-attribution-models-api/) — The Attribution Models API from Northbeam — 1 operation(s) for attribution models.
- [Northbeam Breakdowns API](https://apis.io/apis/northbeam/northbeam-breakdowns-api/) — The Breakdowns API from Northbeam — 1 operation(s) for breakdowns.
- [Northbeam Data Export API](https://apis.io/apis/northbeam/northbeam-data-export-api/) — The Data Export API from Northbeam — 2 operation(s) for data export.
- [Northbeam Metrics API](https://apis.io/apis/northbeam/northbeam-metrics-api/) — The Metrics API from Northbeam — 1 operation(s) for metrics.
- [Northbeam Orders API](https://apis.io/apis/northbeam/northbeam-orders-api/) — The Orders API from Northbeam — 2 operation(s) for orders.
- [Northbeam Spend API](https://apis.io/apis/northbeam/northbeam-spend-api/) — The Spend API from Northbeam — 1 operation(s) for spend.
- [Northbeam Spend Hourly API](https://apis.io/apis/northbeam/northbeam-spend-hourly-api/) — The Spend Hourly API from Northbeam — 1 operation(s) for spend hourly.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [Northbeam](https://apis.io/servers/northbeam/northbeam/)

#### JSON-LD (1)

Linked-data contexts for the provider's vocabulary.

- [Northbeam Context](https://apis.io/jsonld/northbeam/northbeam-context/)

#### Vocabulary (1)

The provider's domain terms, defined.

- [Northbeam API Vocabulary](https://apis.io/vocabularies/northbeam/northbeam-vocabulary/)

#### Overlays (11)

OpenAPI overlays, including conversational phrasing for each operation.

- [Northbeam Attribution Models Api Phrasing Overlay](https://apis.io/overlays/northbeam/northbeam-attribution-models-api-phrasing-overlay/)
- [Northbeam Breakdowns Api Phrasing Overlay](https://apis.io/overlays/northbeam/northbeam-breakdowns-api-phrasing-overlay/)
- [Northbeam Data Export Api Phrasing Overlay](https://apis.io/overlays/northbeam/northbeam-data-export-api-phrasing-overlay/)
- [Northbeam Data Export V1 Overlay](https://apis.io/overlays/northbeam/northbeam-data-export-v1-overlay/)
- [Northbeam Metrics Api Phrasing Overlay](https://apis.io/overlays/northbeam/northbeam-metrics-api-phrasing-overlay/)
- [Northbeam Orders Api Phrasing Overlay](https://apis.io/overlays/northbeam/northbeam-orders-api-phrasing-overlay/)
- [Northbeam Orders V1 Overlay](https://apis.io/overlays/northbeam/northbeam-orders-v1-overlay/)
- [Northbeam Orders V2 Overlay](https://apis.io/overlays/northbeam/northbeam-orders-v2-overlay/)
- [Northbeam Spend Api Phrasing Overlay](https://apis.io/overlays/northbeam/northbeam-spend-api-phrasing-overlay/)
- [Northbeam Spend Hourly Api Phrasing Overlay](https://apis.io/overlays/northbeam/northbeam-spend-hourly-api-phrasing-overlay/)
- [Northbeam Spend V1 Overlay](https://apis.io/overlays/northbeam/northbeam-spend-v1-overlay/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Northbeam Authentication](https://apis.io/security/northbeam/northbeam-authentication/)
- [Northbeam Domain Security](https://apis.io/security/northbeam/northbeam-domain-security/)
- [Northbeam Trust Center](https://apis.io/security/northbeam/northbeam-trust-center/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Northbeam Plans Pricing](https://apis.io/plans/northbeam/northbeam-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Northbeam Rate Limits](https://apis.io/rate-limits/northbeam/northbeam-rate-limits/)

#### FinOps (1)

What the API costs to run, by operation.

- [Northbeam Finops](https://apis.io/finops/northbeam/northbeam-finops/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.northbeam.io)
- [Documentation](https://docs.northbeam.io)
- [Getting started](https://docs.northbeam.io/docs/getting-started)
- [API reference](https://docs.northbeam.io/reference)
- [Pricing](https://www.northbeam.io/pricing)
- [Sign up](https://www.northbeam.io/demo)
- [Support](https://www.northbeam.io/submit-a-support-ticket)
- [Blog](https://www.northbeam.io/blog)
- [Compliance](https://www.northbeam.io/data-security)
- [Terms of service](https://www.northbeam.io/terms-conditions)
- [Privacy policy](https://www.northbeam.io/privacy)

## API Evangelist rating

**Kin Score 65.2** — strong · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 80.0 |
| Contract quality | 55.9 |
| Contract governance | 31.8 |
| Operational transparency | 36.8 |
| Developer ergonomics | 55.4 |
| Access clarity | 92.1 |

**Agent readiness 57.7** — agent-native

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | verified |
| Auth Clarity | served (partial credit) |
| Idempotency | documented (partial credit) |
| Error Semantics | verified |
| OpenAPI Examples | partial |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | — |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | verified |
| Dynamic Client Registration | yes |
| Agentic Commerce | — |
| Pagination Ratio | paginated |
| Operation Distinctiveness | distinct |

**Regulatory alignment 39.2** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/northbeam/](https://apis.io/providers/northbeam/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Northbeam: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/northbeam/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
