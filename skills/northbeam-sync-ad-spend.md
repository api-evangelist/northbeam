---
name: northbeam-sync-ad-spend
description: Upload ad spend from channels Northbeam does not integrate with natively, at daily or hourly grain, so those channels appear with real ROAS instead of being invisible.
api: northbeam:spend-api
base_url: https://api.northbeam.io/v1
sandbox_url: https://api-uat.northbeam.io/v1
spec: openapi/northbeam-spend-v1-openapi.yml
operations:
  - POST /spend
  - GET /spend
  - DELETE /spend
  - POST /spend_hourly
  - GET /spend_hourly
  - DELETE /spend_hourly
generated: '2026-08-13'
method: generated
source: openapi/northbeam-spend-v1-openapi.yml + https://docs.northbeam.io/docs/spend-api-best-practices-and-limits.md
---

# Sync ad spend to Northbeam

Northbeam natively integrates with the large ad platforms. This API is for everything else —
an affiliate network, a TV or podcast buy, an influencer program, a regional platform. Without
it, spend on those channels does not exist in Northbeam and their ROAS cannot be computed.

## Headers

```
Authorization: <your API key>
Data-Client-ID: <your account UUID>
Content-Type: application/json
```

Rehearse against `https://api-uat.northbeam.io/v1` — the spec describes it as
"Production Equivalent (provided for Customer Testing ONLY, spend submitted here does not get
used in attribution)".

## The rule that makes this work at all

Spend rows join to website events **by matching four fields against the customer's ad object
tagging and UTM parameters**:

- `platform_name`
- `campaign_id`
- `adset_id`
- `ad_id`

If those four do not match what lands in the UTMs on the click, Northbeam has spend it cannot
attribute and revenue it cannot explain. Get this agreed before writing any code. Platforms
that have no adset or ad layer may leave those fields empty — that is expected, not an error.

## 1. Daily spend — `POST /spend`

Body is `{"data": [ ...SpendUpsertInput ]}`.

Per row: `date` (ISO date, required), `platform_name` (required), `campaign_id` (required),
`campaign_name`, `platform_account_id` (defaults to `""`; used to segregate ad objects per ad
platform account), `adset_id` / `adset_name`, `ad_id` / `ad_name`, spend and currency.

This is an **upsert** on the natural key (date + platform/campaign/adset/ad). Re-sending a
corrected row for the same day overwrites it. Replaying an unchanged batch is safe.

## 2. Hourly spend — `POST /spend_hourly`

Same shape, except the time key is `hour_start_iso` — a date-time **in UTC**, e.g.
`1970-01-01T00:00:00-0000`. Use this only when the customer's plan includes hourly conversion
data and they actually buy on that cadence; otherwise daily is cheaper and less error-prone.

Daily and hourly are separate collections with separate endpoints. Writing the same spend to
both double-counts it.

## 3. Read and delete

`GET /spend` and `GET /spend_hourly` list records; `DELETE /spend` and `DELETE /spend_hourly`
remove them by the same natural key. Deleting is the correct way to fix spend attributed to
the wrong campaign — overwriting only helps when the key is unchanged.

## 4. Limits and cadence

- **1,000 spend records per call.** Split larger loads.
- Northbeam states there are **no concurrency and no daily/hourly rate limits** on this API —
  the batch size is the only documented constraint. Be a good citizen anyway.
- Sync at least **2x** the frequency of the customer's data pipeline. If their pipeline runs
  hourly and spend arrives once a day, same-day numbers will be unusable for the channels
  covered by this API, even though historical numbers will be fine.

## Errors

| Status | Meaning |
|---|---|
| `401` | Authentication failed — check both headers |
| `422` | The request body is invalid |
| `4XX` / `5XX` | declared as wildcard ranges in the spec: "An unexpected error occurred" |

The Spend API error envelope is a validation shape: `{"detail": [{"loc": [...], "msg": "...",
"type": "..."}]}`. `loc` gives you the exact failing field path. This is a **third** envelope,
different from both Orders and Data Export — see `errors/northbeam-problem-types.yml`.

## Do not

- Do not invent `platform_name` values per run. Pick one canonical string per channel and use
  it in both the spend upload and the UTM tagging, forever.
- Do not write the same spend to both `/spend` and `/spend_hourly`.
- Do not exceed 1,000 records per call and expect a partial success.
