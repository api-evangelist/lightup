---
name: Triage a Lightup data quality incident
description: Work a data quality incident from the incident list through root cause on the underlying metric and monitor, and record the finding as a comment on the incident.
api: openapi/lightup-dashboard-openapi.json
operations:
  - GET /api/v0/ws/{workspace_id}/incidents/
  - GET /api/v0/ws/{workspace_id}/incidents/{incident_uuid}
  - GET /api/v0/ws/{workspace_id}/incidents/{incident_uuid}/comments
  - POST /api/v0/ws/{workspace_id}/incidents/{incident_uuid}/comments
  - GET /api/{api_version}/ws/{workspace_id}/monitors/{filter_uuid}
  - GET /api/{api_version}/ws/{workspace_id}/monitors/{filter_uuid}/slices
  - GET /api/{api_version}/ws/{workspace_id}/metrics/{metric_uuid}/datapoints
---

# Triage a Lightup data quality incident

Every step is identified by HTTP method and path exactly as published in
`openapi/lightup-dashboard-openapi.json` — the spec declares no `operationId` values.

Note the version split: incidents live under the fixed `/api/v0/` prefix, while
metrics and monitors use the templated `/api/{api_version}/` prefix. Do not
normalize one to the other.

## Steps

1. **List open incidents.**
   `GET /api/v0/ws/{workspace_id}/incidents/`. Bound the window with `start_ts`
   and `end_ts`, and narrow to a monitor with `monitor_uuids` or a metric with
   `metric_uuids`. Page with `limit` and `offset`.

2. **Pull the incident detail.**
   `GET /api/v0/ws/{workspace_id}/incidents/{incident_uuid}` for the full
   breakdown, including which monitor fired and on which metric.

3. **Read the monitor that fired.**
   `GET /api/{api_version}/ws/{workspace_id}/monitors/{filter_uuid}`. Establish
   whether it is a manual threshold or an anomaly-detection monitor, and whether
   it is live, training, paused or errored — a monitor still training is a common
   source of noisy incidents.

4. **Check the slices.**
   `GET /api/{api_version}/ws/{workspace_id}/monitors/{filter_uuid}/slices` to see
   whether the breach is confined to one dimensional slice or is table-wide.
   Slices can carry their own training periods and alert settings.

5. **Read the underlying time series.**
   `GET /api/{api_version}/ws/{workspace_id}/metrics/{metric_uuid}/datapoints`
   across a window that spans well before the incident, so a genuine shift can be
   distinguished from a single outlier or a collection gap.

6. **Read existing discussion before adding to it.**
   `GET /api/v0/ws/{workspace_id}/incidents/{incident_uuid}/comments`.

7. **Record the finding.**
   `POST /api/v0/ws/{workspace_id}/incidents/{incident_uuid}/comments` with a
   comment stating what changed, the evidence, and the recommended action.

## Rules

- Distinguish a **data** problem from a **monitor** problem before recommending a
  fix. A flat-lined metric with no datapoints is usually a broken collection or
  datasource connection, not a data quality regression.
- Do not silently retune or delete a monitor to make an incident go away. Propose
  the change and let a workspace Admin or Editor apply it — the workspace may
  require approval for monitor changes.
- Commenting is a write. There is no idempotency contract on this API, so a
  retried `POST` posts a duplicate comment. Verify with the list operation before
  re-posting.
- A `403` means the caller lacks access to that workspace. Report it rather than
  attempting the same call against other workspaces.
