---
name: Plan and schedule a Voltair inspection mission
description: >-
  Create inspection sites and schedule an autonomous drone mission over them,
  then track it to completion via events and webhooks.
api: openapi/voltair-api-openapi-original.yml
operations: [getOrganization, createSite, bulkCreateSites, createMission, addMissionSites, getMission, listSiteVisits, createWebhook]
generated: '2026-07-21'
method: generated
---

# Plan and schedule a Voltair inspection mission

Voltair's API is organization-scoped: authenticate with a Cognito Bearer JWT
or an organization API key in the `X-API-Key` header (see
`authentication/voltair-authentication.yml`).

## Steps

1. **Confirm your tenant** — `getOrganization` (`GET /organization`) returns
   the organization for the authenticated caller.
2. **Register what to inspect** — `createSite` (`POST /sites`) for a single
   site or `bulkCreateSites` (`POST /sites/bulk`) for many. Sites carry
   GeoJSON geometry (point/area/line), a `siteType`, tags, and priority.
   Send an `Idempotency-Key` (uuid) header on these POSTs — keys are valid
   48 hours and replay the original response on retry.
3. **Create the mission** — `createMission` (`POST /missions`) with name,
   `scheduledFor` (Unix **milliseconds**, like every timestamp on this API),
   `detailLevel`, sensor requirements (RGB / thermal / LiDAR), and optional
   `recurrence`.
4. **Attach sites** — `addMissionSites` (`POST /missions/{missionId}/sites`);
   remove with `removeMissionSites` if plans change.
5. **Subscribe to progress** — `createWebhook` (`POST /webhooks`) for
   `mission.scheduled`, `mission.started`, `mission.completed`,
   `mission.failed`, `processing.started`, `processing.completed`. Verify
   payloads with the HMAC-SHA256 `secret`.
6. **Track execution** — `getMission` for status; `listSiteVisits`
   (`GET /site-visits`) shows per-site capture progress and recapture state.

## Rules

- Paginate lists with `limit` (default 50, max 200) and the opaque `cursor`
  from `meta.cursor`; stop when `meta.cursor` is null.
- On `429`, back off per `Retry-After`; window state is in `X-RateLimit-*`.
- Errors come as `{error: {code, message, details}}`; keep `X-Request-Id`
  from the response headers when reporting issues.
- Every mutation returns a `transactionId` — a mistake can be reversed with
  `undoTransaction` (`POST /transactions/{transactionId}/undo`).
