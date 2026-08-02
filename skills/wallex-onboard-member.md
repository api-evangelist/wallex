---
name: Onboard and screen a partner member
description: Register a member account, submit KYC/CDD data and documents, and trigger compliance screening.
api: llms/wallex-llms.txt
operations: [authenticate, sign-up, customer-due-diligence-update, document-create, user-screening]
generated: '2026-07-21'
method: generated
source: https://docs.wallex.asia/docs/api-docs/users
---

# Onboard and screen a partner member

Partners (B2C classification) onboard their own end-users as members. This flow registers a member, completes due diligence, and submits for compliance review.

## Rules the agent must follow
- `X-Api-Key` on every request (403 if missing); bearer token for restricted calls (401 if missing).
- Sign Up and Lite sign-up are **disabled by default** — enable with Wallex first (contact@wallextech.com). Lite accounts are limited to first-party collections and payouts to the partner's own account.
- `customer-due-diligence-update` and `user-detail-update` are partial: omitted fields are left unchanged.
- Use `entity-types-listing` and `products-usage-listing` to get the valid `key` values before updating CDD attributes.
- Documents are uploaded via a generated URL: call `document-create` to get the upload URL, then follow the File Upload instructions.

## Steps
1. **authenticate** — mint a bearer token.
2. **sign-up** (or `sign-up-company-lite` / `sign-up-individual-lite`) — register the member account.
3. **customer-due-diligence-update** / **company-update** / **user-detail-update** — supply KYC data (entity type, product usage, company registration, user profile).
4. **document-create** — generate an upload URL and attach supporting documents (incorporation certificate, proof of address, ID).
5. **user-screening** — trigger compliance screening. Status moves to `pending_approval`; the outcome (`approved`, `document_needs_resubmission`, `withdrawn`, `terminated`, `closed`) arrives via the `user.status` webhook.
