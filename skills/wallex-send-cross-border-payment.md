---
name: Send a cross-border payment
description: Quote, create, and fund a simple payment to a beneficiary through the Wallex Partner API.
api: llms/wallex-llms.txt
operations: [authenticate, payment-quote, payment-create, payment-fund, payment-retrieve]
generated: '2026-07-21'
method: generated
source: https://docs.wallex.asia/docs/send-first-payment
---

# Send a cross-border payment

Use this flow to pay a beneficiary. Funds are only moved on the explicit **fund** step, so quote and create are safe to call while you confirm details.

## Prerequisites
- An `X-Api-Key` (sent on **every** request) plus `accessKeyId` / `secretAccessKey`, issued by Wallex (contact@wallextech.com).
- The beneficiary must already exist (`beneficiary-create`; call `beneficiary-requirement` first to learn required fields for the currency/country).

## Rules the agent must follow
- Send `X-Api-Key` on every request; missing/invalid → `403`.
- Restricted calls need `Authorization: Bearer <token>`; missing → `401`.
- Tokens last 8 hours — refresh via `authenticate` at any time.
- No idempotency-key exists; do not blind-retry `payment-fund`. On a network error, reconcile with `payment-retrieve` before retrying.
- As a Partner, scope the call to a member with `onBehalfOfAccount` (recommended) or `onBehalfOfUser` (must be an approved user).
- Rate limit: 5 req/s, 432,000/day.

## Steps
1. **authenticate** — `POST /v2/authenticate` with `accessKeyId` + `secretAccessKey`; store the returned `token`.
2. **payment-quote** — retrieve a rate for the currency pair and amount to lock pricing.
3. **payment-create** — create the payment against the quote and beneficiary. It starts in `awaiting_funds`.
4. **payment-fund** — fund the payment to trigger the transfer. It advances through `under_review → processing → in_transit → completed` (or `failed` / `rejected`, which refund via Funding).
5. **payment-retrieve** — poll or, better, subscribe to the `payment.status` webhook to track terminal state.
