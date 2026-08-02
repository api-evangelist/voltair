---
name: Operate Voltair webhooks and API keys safely
description: >-
  Provision organization API keys, subscribe to mission/processing events,
  verify signed deliveries, and audit every change.
api: openapi/voltair-api-openapi-original.yml
operations: [createApiKey, listApiKeys, deleteApiKey, createWebhook, testWebhook, listWebhookDeliveries, rotateWebhookSecret, listEvents, listTransactions, undoTransaction]
generated: '2026-07-21'
method: generated
---

# Operate Voltair webhooks and API keys safely

## Steps

1. **Provision a key** — `createApiKey` (`POST /api-keys`, send an
   `Idempotency-Key`). The raw key is returned **once**; store it in a
   secret manager. Only the 12-character display `prefix` is retrievable
   later (`listApiKeys`). Revoke with `deleteApiKey`.
2. **Subscribe** — `createWebhook` (`POST /webhooks`) with your endpoint URL
   and the event list (`mission.scheduled|started|completed|failed`,
   `processing.started|completed`). Keep the returned HMAC-SHA256 `secret`
   for signature verification.
3. **Prove the pipe** — `testWebhook` (`POST /webhooks/{webhookId}/test`)
   fires a test delivery; confirm receipt and signature check.
4. **Monitor deliveries** — `listWebhookDeliveries`
   (`GET /webhooks/{webhookId}/deliveries`) exposes statusCode,
   responseTimeMs, and error per attempt; alert on repeated failures.
5. **Rotate secrets** — `rotateWebhookSecret`
   (`POST /webhooks/{webhookId}/rotate-secret`) on schedule or on suspicion
   of compromise; update your verifier atomically.
6. **Audit** — `listEvents` (`GET /events`) is the full audit log (actor,
   before/after deltas, including `api_key` and `webhook` resources);
   `listTransactions` records every mutation, and `undoTransaction` reverses
   a mistaken one.

## Rules

- Auth is Bearer JWT (Cognito) or `X-API-Key`; keys are organization-scoped.
- Verify webhook payloads with HMAC-SHA256 using the current secret; reject
  unsigned/invalid payloads.
- Cursor-paginate delivery and event lists; respect `429` + `Retry-After`.
