# Lightup

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
