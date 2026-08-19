---
name: northbeam-sync-orders
description: Send e-commerce order and revenue data to Northbeam so it can be used as the ground truth for multi-touch attribution and media mix modeling.
api: northbeam:orders-api
base_url: https://api.northbeam.io/v2
sandbox_url: https://api-uat.northbeam.io/v2
spec: openapi/northbeam-orders-v2-openapi.yml
operations:
  - POST /orders
  - PATCH /orders (operationId patchOrders)
  - GET /orders
  - POST /orders/aliases (operationId addOrderAliases)
generated: '2026-08-13'
method: generated
source: openapi/northbeam-orders-v2-openapi.yml + https://docs.northbeam.io/docs/orders-api.md
---

# Sync orders to Northbeam

Northbeam attributes ad spend to revenue. Orders are the revenue side. If the orders are
wrong, late, or duplicated, every ROAS and CAC number the customer looks at is wrong too.

## Before you call anything

Send **both** headers on every request. One of them is not enough and a missing
`Data-Client-ID` presents as a plain `401 Unauthenticated`, not as a distinct error:

```
Authorization: <your API key>
Data-Client-ID: <your account UUID>
Content-Type: application/json
```

Both come from the Northbeam dashboard under `Settings -> API Keys -> Create new API Key`.

Rehearse against `https://api-uat.northbeam.io/v2` first. Northbeam publishes anonymous test
credentials for it — `Authorization: None` and `Data-Client-ID: test` — which run validation
and persist nothing. There are no test-mode vs live-mode key prefixes: **the host is the only
thing separating a rehearsal from a real write.** Assert on the hostname before you send.

## 1. Upsert orders — `POST /orders`

The request body is an array of order objects, `minItems: 1`, `uniqueItems: true`, and the
item schema sets `additionalProperties: false` — an unknown field is rejected, not ignored.

Required on every order: `order_id`, `customer_id`, `time_of_purchase`, `currency`,
`purchase_total`, `tax`, `products`, `shipping_cost`.

Rules that matter more than the field list:

- **`order_id` must be globally unique across all of the customer's orders and must not be
  the customer id.** If the site also fires the Northbeam pixel, this must be the exact same
  id passed to `firePurchaseEvent` / `fireSlimPurchaseEvent`. That match is what joins the
  click to the revenue; a mismatch silently loses the attribution.
- **`customer_id` must not be an email and must not be the order id.**
- `time_of_purchase` is an ISO-8601 timestamp with an offset, e.g. `2022-03-08T01:23:45-08:00`.
- `currency` is an ISO 4217 code; country fields are ISO 3166-1 alpha-2. Both are
  pattern-constrained in `components.schemas._patterns`.
- Send customer email and phone **either** as plaintext (`customer_email`,
  `customer_phone_number`) **or** pre-hashed (`hashed_customer_email`,
  `hashed_customer_phone_number`) — never both for the same field. Hashing is SHA-256 over a
  normalized value; follow https://docs.northbeam.io/docs/hashing-customer-data.md exactly or
  the hashes will not match Northbeam's.

This operation is an **upsert keyed on `order_id`**. Replaying the identical batch is safe and
creates no duplicates. There is no `Idempotency-Key` header and none is needed here.

## 2. Batch and pace the writes

- Hard cap: **1,000 orders per request.**
- Rate limit: **30 MB of request body per API key per rolling 60 seconds**, and that budget is
  *shared* across `POST /v1/orders`, `POST /v2/orders` and `PATCH /v2/orders`. A backfill job
  and a live sync running on the same key will starve each other.
- On exhaustion you get `429` with a `Retry-After` header and
  `{"error": "rate_limit_error", "retry_after_seconds": N}`. Sleep for that many seconds.
  Northbeam publishes **no** `RateLimit-*` budget headers, so you cannot see how close you are
  until you are refused — pace deliberately rather than reactively.
- Northbeam's own guidance is to sync at least **2x** the frequency of the customer's data
  pipeline run.

## 3. Correct existing orders — `PATCH /orders` (`patchOrders`)

Only for orders that already exist. This is the one place the "upsert" mental model breaks:

- **The entire batch fails if any `order_id` does not exist** or any entry fails validation.
- Only the fields present in the payload change. An **explicit `null` clears** an optional
  field; an **omitted** field is left alone.
- `order_tags` and `discount_codes` are **replaced wholesale**, not merged per item.
- `products` and `refunds` **cannot be patched at all.** To change either, re-submit the whole
  order with `POST /orders`.
- `413 Payload too large` means split the batch, not retry it.

## 4. Attach alternate ids — `POST /orders/aliases` (`addOrderAliases`)

Use when the same order is known by a second id somewhere else (an OMS number, a marketplace
id). Additive and idempotent — aliases that already exist are silently ignored. Returns `404`
if any `order_id` is unknown.

## 5. Read back — `GET /orders`

Bounded by `start_date` and `end_date` query parameters. There is **no pagination anywhere in
this API** — no cursor, no offset, no `Link` header. Narrow the date window instead of asking
for a page. For anything analytical, use the Data Export API rather than this endpoint.

## Errors you will actually hit

| Status | Meaning | Do this |
|---|---|---|
| `400` with no message | malformed JSON | fix the serializer, do not retry blindly |
| `400` with `response` | per-order validation failure | read `response[].json_path` and `response[].message`, fix those orders, resend just them |
| `401` | bad or missing `Authorization` **or** `Data-Client-ID` | check both headers before assuming the key is wrong |
| `413` | payload too large | halve the batch |
| `429` | rate limited | sleep `retry_after_seconds`, then resume |
| `500` | Northbeam-side | retry with backoff; the upsert makes retries safe |

The Orders error envelope is `{status, response}` where `response` is a JSON-encoded string.
Note that the Spend API and the Data Export API use **different** envelopes — do not write one
parser for all three. See `errors/northbeam-problem-types.yml`.

## Do not

- Do not assume `PATCH` will create a missing order. It will fail the whole batch.
- Do not reuse `customer_id` as `order_id`, or vice versa.
- Do not send both plaintext and hashed variants of the same identity field.
- Do not point a backfill at `api.northbeam.io` while testing. Use `api-uat.northbeam.io`.
- Do not start on `/v1/orders`. Its published spec is titled "API - Orders - V1 (Deprecated)".
