# Lightup

Lightup Data, Inc. is an enterprise data quality and data observability platform that lets data teams detect, diagnose, and resolve data issues across cloud and hybrid data stacks. Checks (DQIs) are pushed down to the customer's own datasources — Snowflake, Databricks, Redshift, BigQuery, Postgres, Athena, Oracle, Teradata, SAP HANA, Dremio, Incorta, ksqlDB, S3 and Azure blob storage — so no customer source data is copied into the Lightup environment.

- Website: https://www.lightup.ai/
- Documentation: https://docs.lightup.ai/docs
- API reference: https://docs.lightup.ai/reference
- GitHub: https://github.com/lightup-data

Backed by: a16z

## API

The Lightup Dashboard API is an OpenAPI 3.1.0 REST API covering workspaces, datasources, metrics, monitors, incidents, integrations, data catalogs, dashboard charts and users. It is deployed per customer at `https://app.{clusterId}.lightup.ai`. Requests carry a JWT access token obtained by exchanging the refresh token from a downloaded API credential file.

## Artifacts in this repo

| Artifact | Path | Method |
|---|---|---|
| OpenAPI 3.1.0 (54 operations) | `openapi/` | searched |
| llms.txt | `llms/` | searched |
| MCP server (Lightup Agentic, 41 tools) | `mcp/` | searched |
| Packages / SDKs (3 PyPI packages) | `packages/` | searched |
| CLI (`lightctl`) | `cli/` | searched |
| Authentication profile | `authentication/` | searched |
| Changelog (release notes) | `changelog/` | searched |
| Conformance (SOC 2 Type 2, ISAE 3000) | `conformance/` | searched |
| Sandbox / trial | `sandbox/` | searched |
| Vulnerability disclosure, trust center, domain security | `security/` | searched / probed |
| Well-known probe (no surface published) | `well-known/` | searched |
| API conventions | `conventions/` | derived |
| Lifecycle | `lifecycle/` | derived |
| Data model | `data-model/` | derived |
| OpenAPI Overlay | `overlays/` | generated |
| Agent Skills (4) | `skills/` | generated |
| Agentic access (54 operations) | `agentic-access/` | generated |

Not published by Lightup, so deliberately absent: AsyncAPI / webhooks (event delivery is via outbound notification connectors), OAuth scopes (auth is a JWT refresh/access exchange, not OAuth), an error catalog (no 4xx/5xx documented and no error reference), gRPC, embedded UI components, and decline codes (not a payments provider).
