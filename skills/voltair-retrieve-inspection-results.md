---
name: Retrieve Voltair inspection results, defects, and media
description: >-
  Pull inspection outcomes, triage defects by priority, and download captured
  RGB/thermal/LiDAR media after a mission completes.
api: openapi/voltair-api-openapi-original.yml
operations: [listInspections, listAssetInspections, listDefects, updateDefect, bulkUpdateDefectPriority, listMedia, getMedia, createMediaDownloadUrl, downloadMissionMediaZip, downloadDefectEvidence]
generated: '2026-07-21'
method: generated
---

# Retrieve Voltair inspection results, defects, and media

Run this after a `mission.completed` / `processing.completed` webhook (or by
polling `getMission`). Authenticate with Bearer JWT or `X-API-Key`.

## Steps

1. **Site-level outcomes** — `listInspections` (`GET /inspections`), then
   `getInspection` for detail; asset-level outcomes via
   `listAssetInspections` (`GET /asset-inspections`).
2. **Findings** — `listDefects` (`GET /defects`) returns defects with
   `defectPriority`, review state, and evidence. Update one with
   `updateDefect` (`PATCH /defects/{defectId}`) or re-prioritize in bulk via
   `bulkUpdateDefectPriority` (`POST /defects:bulk-priority`) — both accept
   an `Idempotency-Key` header.
3. **Evidence images** — `downloadDefectEvidence`
   (`GET /defect-evidence/{evidenceId}/download`) fetches the annotated
   media behind a defect.
4. **Captured media** — `listMedia` (`GET /media`) filtered per mission/site;
   `getMedia` for one item's capture metadata (device, orientation, geo
   location/path, thermal range).
5. **Bulk download** — `createMediaDownloadUrl` (`POST /media/download-url`)
   for signed URLs, or `downloadMissionMediaZip` (`GET /media/download.zip`)
   for a whole mission's archive; single files via
   `downloadMissionMediaSingle` (`GET /media/{mediaId}/download`).

## Rules

- Timestamps (`capturedAt`, `createdAt`, ...) are Unix **milliseconds** UTC.
- Cursor-paginate every list (`limit` max 200, follow `meta.cursor`).
- Honor `Retry-After` on `429`; errors arrive as `{error: {code, message}}`.
- Defect/media mutations return `transactionId` and are reversible with
  `undoTransaction`.
