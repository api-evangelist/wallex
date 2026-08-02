---
name: Receive funds via a virtual collection account
description: Provision a virtual account and/or a collection request to receive funds into a Wallex wallet.
api: llms/wallex-llms.txt
operations: [authenticate, supported-currencies, collection-account-create, collection-request-create, collection-request-retrieve]
generated: '2026-07-21'
method: generated
source: https://docs.wallex.asia/docs/api-docs/collections
---

# Receive funds via a virtual collection account

Collect payments from third parties into a Wallex wallet. Once a collection completes, the account balance is credited.

## Rules the agent must follow
- `X-Api-Key` on every request (403 if missing); `Authorization: Bearer <token>` for restricted calls (401 if missing).
- `collection-account-create` is member-only by default; partner-account use must be enabled by Wallex (contact@wallextech.com).
- Check `supported-currencies` before provisioning to confirm the currency + provider is available.
- Subscribe to `collection.status`, `collection_account.status`, and `collection_request.status` webhooks rather than polling.

## Steps
1. **authenticate** — mint a bearer token.
2. **supported-currencies** — confirm the target currency and its currency provider are supported.
3. **collection-account-create** — generate a virtual account (VA) in the chosen currency, assigned to a user. Share the VA details with the payer.
4. *(alternative)* **collection-request-create** — issue a collection request to receive a payment in the client's local currency with automatic conversion into your designated wallet currency.
5. **collection-request-retrieve** — check status; in sandbox you may use `collection-request-paid` to simulate a Full Paid without a real transfer.
