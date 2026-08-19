---
name: northbeam-export-attribution-data
description: Pull Northbeam attribution performance metrics (ROAS, CAC, revenue, transactions) for a date range and breakdown, using the asynchronous submit-then-poll Data Export API.
api: northbeam:data-export-api
base_url: https://api.northbeam.io/v1/exports
spec: openapi/northbeam-data-export-v1-openapi.yml
operations:
  - GET /metrics
  - GET /breakdowns
  - GET /attribution-models
  - POST /data-export
  - GET /data-export/result/{export_id}
generated: '2026-08-13'
method: generated
source: openapi/northbeam-data-export-v1-openapi.yml + https://docs.northbeam.io/docs/northbeam-api-data-export-1.md
---

# Export attribution data from Northbeam

This is the read side of Northbeam. It is **asynchronous**: you submit an export config, get
an `export_id`, then poll for the result. There is no webhook and no callback — polling is the
only completion signal Northbeam offers.

Data Export requires a **Professional or Enterprise** plan; it is listed on the pricing page
as part of "Unlimited users, data exports, integrations, and MCP access".

## Headers

```
Authorization: <your API key>
Data-Client-ID: <your account UUID>
Content-Type: application/json
```

Note this API declares **only the production server** — unlike Orders and Spend, there is no
UAT host for exports. You cannot rehearse a read against a sandbox.

## 1. Discover the vocabulary first — do not guess enum values

Three reference endpoints exist precisely so an agent does not have to invent field names.
Call them before building a request, and cache the results:

- `GET /metrics` — every metric you may put in `metrics[]`
- `GET /breakdowns` — every label you may put in `breakdowns[]`
- `GET /attribution-models` — every value `attribution_option` accepts

These are static reference data. Caching them locally is the single biggest thing you can do
to stay inside the rate limits.

## 2. Submit the export — `POST /data-export`

Body fields come from `Export_Request_Base`:

- `level` — `platform` | `campaign` | `adset` | `ad` (default `platform`)
- `time_granularity` — `MONTHLY` | `WEEKLY` | `DAILY` | `HOURLY` (default `DAILY`)
- `period_type` — a relative window (`LAST_7_DAYS`, `YESTERDAY`, `MONTH_TO_DATE`,
  `LAST_12_MONTHS`, and ~35 others) or `FIXED`. Default `LAST_7_DAYS`.
- `period_options` — **required when `period_type` is `FIXED`**, ignored otherwise
- `attribution_option` — a value from `GET /attribution-models`
- `breakdowns[]` — values from `GET /breakdowns`
- `metrics[]` — values from `GET /metrics`

Choose a sink. The spec defines three request shapes:

- `Export_To_Northbeam_Documents` — the default; results are fetched back through the API
- `Export_To_GCS_Bucket` — delivered to the customer's Google Cloud Storage bucket
- `Export_To_S3_Bucket` — delivered to the customer's Amazon S3 bucket

For anything large or recurring, prefer a bucket sink: it removes the polling loop and the
result-size problem entirely.

Success is `201` and returns the `export_id`.

## 3. Poll for the result — `GET /data-export/result/{export_id}`

Status transitions through `PENDING` -> `SUCCESS` or `ERROR`. Poll on a backoff; do not
hot-loop. `PENDING` is not an error and an export can legitimately stay pending for a while
on wide date ranges at `ad` level with `HOURLY` granularity.

## 4. Respect the limits — they are asymmetric

| Endpoint | Limit |
|---|---|
| `POST /data-export` | **60 requests / minute** |
| `GET /data-export/result/{export_id}` | 100 requests / second |
| `GET /breakdowns` | 100 requests / second |
| `GET /attribution-models` | 100 requests / second |
| `GET /metrics` | 100 requests / second |

Submission is ~360x tighter than polling. The failure mode is a fan-out job that submits one
export per campaign: consolidate into fewer, wider exports with a `breakdowns[]` instead.

Exhaustion returns `429 Too many requests`. Unlike the Orders API, no `retry_after_seconds`
body field is documented here — back off exponentially.

## Errors

| Status | Meaning |
|---|---|
| `401` | Unauthorized — check both `Authorization` and `Data-Client-ID` |
| `422` | Invalid Body Params — usually an enum value not present in `/metrics`, `/breakdowns` or `/attribution-models` |
| `429` | Too many requests |
| `500` | Internal server error — retry with backoff |

The Data Export error envelope is `{"error": "<string>"}`. It is **not** the Orders envelope
and **not** the Spend envelope. See `errors/northbeam-problem-types.yml`.

## Cost-aware defaults

- Cache `/metrics`, `/breakdowns` and `/attribution-models` — they change rarely and each
  refetch spends a request you may want later.
- Widen `time_granularity` before widening the date range. `HOURLY` at `ad` level over
  `LAST_12_MONTHS` is the most expensive request in this API.
- Schedule bulk exports off-peak so a scheduled job and an interactive one do not collide on
  the 60/minute submission budget.
