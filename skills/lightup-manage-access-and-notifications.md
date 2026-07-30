---
name: Manage Lightup workspace access and alert routing
description: Onboard users into a Lightup workspace with the right role and wire the workspace's incident notifications to Slack, PagerDuty or the other supported targets.
api: openapi/lightup-dashboard-openapi.json
operations:
  - GET /api/v0/users/
  - POST /api/v0/users/
  - PATCH /api/v0/users/{user_id}
  - GET /api/v0/ws/{workspace_id}/users/
  - POST /api/v0/ws/{workspace_id}/users/
  - PATCH /api/v0/ws/{workspace_id}/users/{user_id}
  - DELETE /api/v0/ws/{workspace_id}/users/{user_id}
  - GET /api/{api_version}/ws/{workspace_id}/integrations/
  - POST /api/{api_version}/ws/{workspace_id}/integrations/
---

# Manage Lightup workspace access and alert routing

Every step is identified by HTTP method and path exactly as published in
`openapi/lightup-dashboard-openapi.json` — the spec declares no `operationId` values.

User management lives under the fixed `/api/v0/` prefix; integrations use the
templated `/api/{api_version}/` prefix.

## Role model

Lightup separates **app roles** from **workspace roles**
(https://docs.lightup.ai/docs/roles):

- App roles: **App Admin** (invite users, set app roles, create workspaces),
  **App Editor** (create workspaces only), **App Viewer** (can be invited to
  workspaces).
- Workspace roles: **Admin** (everything, including datasources), **Editor**
  (everything except managing datasources), **Viewer**, **Observer** (most
  restricted).

You need App Admin to invite a new user to the instance. Adding an existing user
to a workspace requires the appropriate workspace or app permission.

## Steps — access

1. **Check whether the user already exists.**
   `GET /api/v0/users/` before inviting, so you do not duplicate an account.

2. **Invite a new application user.**
   `POST /api/v0/users/`. Expect `201 Created`.

3. **Adjust an app role or expiration if needed.**
   `PATCH /api/v0/users/{user_id}`.

4. **List current workspace members.**
   `GET /api/v0/ws/{workspace_id}/users/`.

5. **Add the user to the workspace.**
   `POST /api/v0/ws/{workspace_id}/users/` with the intended workspace role.
   Grant the least privilege that lets the person do their job — Viewer or
   Observer for consumers of data quality signal, Editor for people authoring
   metrics and monitors, Admin only for people who must manage datasources.

6. **Change or revoke.**
   `PATCH /api/v0/ws/{workspace_id}/users/{user_id}` to change the workspace role,
   `DELETE /api/v0/ws/{workspace_id}/users/{user_id}` to remove access. Delete
   returns `204 No Content`.

## Steps — notification routing

7. **List existing integrations.**
   `GET /api/{api_version}/ws/{workspace_id}/integrations/`. Integrations are
   per-workspace, so the same Slack channel must be configured separately in each
   workspace that should alert into it.

8. **Create the integration.**
   `POST /api/{api_version}/ws/{workspace_id}/integrations/`. Supported targets
   are Slack, PagerDuty, Jira, ServiceNow, Microsoft Teams, OpsGenie and email
   lists (https://docs.lightup.ai/docs/integrations-overview). Expect `201`.

## Rules

- Access changes are consequential and **not idempotent** — this API publishes no
  idempotency key. A retried invite or workspace-add can duplicate. Always
  re-list before retrying a failed write.
- Do not escalate a role beyond what was asked. Adding someone as workspace Admin
  when Editor was requested hands them datasource credentials management.
- Deletion is immediate and returns `204` with no body. Confirm the intended
  `user_id` against the workspace member list before calling `DELETE`.
- Data **catalog** integrations (Alation, Collibra, Atlan) are instance-level, not
  workspace-level, and live at `/api/{api_version}/system/integrations` under the
  Catalogs tag. They require the App Admin role. Do not confuse the two surfaces.
