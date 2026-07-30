---
name: Authenticate to Lightup and explore data quality state
description: Exchange a Lightup API credential for an access token, then list workspaces, datasources and metrics to establish what data quality coverage exists.
api: openapi/lightup-dashboard-openapi.json
operations:
  - POST /api/v1/token/refresh/
  - GET /api/{api_version}/workspaces/
  - GET /api/{api_version}/ws/{workspace_id}/sources/
  - GET /api/{api_version}/ws/{workspace_id}/metrics/
  - GET /api/{api_version}/ws/{workspace_id}/monitors/
---

# Authenticate to Lightup and explore data quality state

The Lightup OpenAPI declares no `operationId` values, so every step below is
identified by its HTTP method and path exactly as published in
`openapi/lightup-dashboard-openapi.json`.

## Before you start

- Lightup is deployed per customer. The API host is `https://app.{clusterId}.lightup.ai`
  where `clusterId` is the customer's own subdomain. Never assume `demo` — that is
  only the placeholder default in the spec's `servers[]` entry.
- You need a credential file (`lightup-api-credential*.json`) downloaded from the
  Lightup UI under the gear icon → **API Credentials** → **Generate API credential**.
  It carries the cluster host and a long-lived `refresh` token.
- If a Lightup MCP server is available, prefer its read tools (`list_workspaces`,
  `list_metrics`, `get_workspace_health`, `get_platform_summary`) — they are
  token-efficient and cover this flow. Fall back to REST when MCP is not connected.

## Steps

1. **Mint an access token.**
   `POST /api/v1/token/refresh/` with body field `refresh` set to the `refresh`
   value from the credential file. Read the `access` field from the response.
   This is the only operation that does not require an `Authorization` header.

2. **Send the token on every subsequent call.**
   Set header `authorization: Bearer <access>`. The token is short-lived; on
   failure re-run step 1 rather than retrying the same token.

3. **List workspaces.**
   `GET /api/{api_version}/workspaces/`. Substitute the cluster's API version for
   `{api_version}`. Everything else is scoped to a `workspace_id` from this list.

4. **List datasources in the workspace.**
   `GET /api/{api_version}/ws/{workspace_id}/sources/` to see which systems are
   connected before reasoning about coverage.

5. **List metrics.**
   `GET /api/{api_version}/ws/{workspace_id}/metrics/`. Page with `limit` and
   `offset`. Narrow with `source_uuids`, `table_uuids`, `names` or `name`.

6. **List monitors.**
   `GET /api/{api_version}/ws/{workspace_id}/monitors/`, filtering with
   `metric_uuids` where you care about a specific metric. Note that monitors are
   called *filters* in the path and are keyed by `filter_uuid`.

## Rules

- Treat a `403` as a workspace-authorization problem, not a bad token — the user
  may simply lack access to that workspace. Say so rather than retrying.
- These list operations paginate with `limit`/`offset` only. There is no cursor
  and no documented total-count field; do not claim a total you have not counted.
- The API documents no rate limits. Still page rather than requesting unbounded
  lists, and do not parallel-fan across every workspace on a large instance.
- Repeat this exploration read-only. Do not create or modify anything in this skill.
