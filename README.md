# Voltair

Voltair (Y Combinator W26, San Francisco) is building a globally distributed network of autonomous drones for Earth observation, starting with power-utility infrastructure inspection. Its Faraday-1 long-range aircraft recharge on Lighthouse-1 pads at utility substations and capture RGB, radiometric-thermal, and LiDAR inspection data on demand.

- Website: https://voltairlabs.com
- API reference: https://api.voltairlabs.com/docs
- OpenAPI: https://api.voltairlabs.com/openapi.yaml (harvested in `openapi/`)

The Voltair API is an organization-scoped infrastructure inspection platform — sites, assets, defects, clusters, missions, visits, media, inspections, an audit/undo transaction log, API keys, and HMAC-signed webhooks — with Idempotency-Key support, cursor pagination, and rate-limit signaling.

This repository is part of the [API Evangelist](https://apievangelist.com) network: `apis.yml` is the APIs.json index, and the artifact directories (`openapi/`, `authentication/`, `conventions/`, `errors/`, `asyncapi/`, `skills/`, `mcp/`, `security/`, ...) carry the enrichment produced from Voltair's published surface.

Backed by: y-combinator
