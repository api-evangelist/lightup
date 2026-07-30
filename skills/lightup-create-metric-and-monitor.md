---
name: Create a Lightup metric and attach a monitor
description: Stand up a new data quality indicator against a connected datasource and attach a threshold or anomaly-detection monitor that raises incidents.
api: openapi/lightup-dashboard-openapi.json
operations:
  - POST /api/v1/token/refresh/
  - GET /api/{api_version}/ws/{workspace_id}/sources/
  - POST /api/{api_version}/ws/{workspace_id}/metrics/
  - GET /api/{api_version}/ws/{workspace_id}/metrics/{pk}
  - GET /api/{api_version}/ws/{workspace_id}/metrics/{metric_uuid}/datapoints
  - POST /api/{api_version}/ws/{workspace_id}/monitors/
  - GET /api/{api_version}/ws/{workspace_id}/monitors/{filter_uuid}
  - POST /api/{api_version}/ws/{workspace_id}/sources/{pk}/trigger
---

# Create a Lightup metric and attach a monitor

Every step is identified by HTTP method and path exactly as published in
`openapi/lightup-dashboard-openapi.json` — the spec declares no `operationId` values.

## Before you start

- Authenticate first (see `lightup-authenticate-and-explore.md`): exchange the
  credential file's `refresh` value at `POST /api/v1/token/refresh/` and send
  `authorization: Bearer <access>` on every call.
- You need at least the **Editor** workspace role to create metrics and monitors,
  and **Admin** to manage datasources. Creating a metric may require approval if
  the workspace admin has enabled monitor/metric approval.
- The published OpenAPI declares no `components.schemas`, so request body shapes
  are not machine-readable. Read the metric-type documentation before constructing
  a body: https://docs.lightup.ai/docs/metric-overview

## Steps

1. **Pick the datasource.**
   `GET /api/{api_version}/ws/{workspace_id}/sources/` and select the
   `source_uuid` the check should run against.

2. **Create the metric.**
   `POST /api/{api_version}/ws/{workspace_id}/metrics/`. Choose the metric type
   that matches the question being asked — null percent, data volume, data delay,
   distribution, conformity, aggregation, row-by-row, or custom SQL. Expect
   `201 Created`. Capture the returned metric UUID.

3. **Confirm the metric.**
   `GET /api/{api_version}/ws/{workspace_id}/metrics/{pk}` using the new UUID.

4. **Collect data before monitoring.**
   A monitor needs history to train against. Either wait for the datasource
   schedule, or force collection with
   `POST /api/{api_version}/ws/{workspace_id}/sources/{pk}/trigger`.

5. **Check datapoints exist.**
   `GET /api/{api_version}/ws/{workspace_id}/metrics/{metric_uuid}/datapoints`
   bounded by `start_ts` and `end_ts`. Do not attach an anomaly monitor to a
   metric with no datapoints — it will sit in training rather than going live.

6. **Create the monitor.**
   `POST /api/{api_version}/ws/{workspace_id}/monitors/`. Use a manual threshold
   monitor when the acceptable band is known, and anomaly detection when it is
   not. Expect `201 Created`; capture the `filter_uuid`.

7. **Verify the monitor.**
   `GET /api/{api_version}/ws/{workspace_id}/monitors/{filter_uuid}` and check it
   is live rather than training, paused or errored.

## Rules

- **There is no idempotency contract.** The API exposes no `Idempotency-Key`
  header or equivalent. A retried `POST` will create a duplicate metric or
  monitor. On an ambiguous failure, re-list and check whether the object was
  created before retrying — never blind-retry a create.
- Creates return `201`, updates return `200`, deletes return `204`. No `4xx`/`5xx`
  responses are documented in the spec, so treat error bodies as untyped and
  surface the raw status and message rather than inventing an error taxonomy.
- Prefer previewing before creating where the surface allows it (the MCP server
  exposes `preview_metric` and `validate_custom_sql`); a metric built on invalid
  SQL fails at collection time, not creation time.
- Monitors are *filters* in the API path and are keyed by `filter_uuid`, not by
  the metric UUID. Do not conflate the two.
