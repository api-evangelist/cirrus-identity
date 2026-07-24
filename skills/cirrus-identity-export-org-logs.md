---
name: Export Cirrus Identity org logs
description: Retrieve authentication and service event logs for an organization from the Cirrus Identity Log API, paging through results with the cursor.
api: openapi/cirrus-identity-log-api-openapi.json
operations: [get_orgs, get_org_logs]
---

# Export Cirrus Identity org logs

Use the Cirrus Identity **Log API** to pull authentication and service events for an
organization you administer.

## Auth
All requests use **HTTP Basic** auth with Log API credentials provisioned in the
Cirrus Console (see conventions/cirrus-identity-conventions.yml). Base URL:
`https://api.cirrusidentity.com/logs/v1`.

## Steps
1. **List the orgUrls you may query** — call `get_orgs` (`GET /orgUrls`). It returns the
   `orgUrl` values your credentials are permitted to use.
2. **Fetch a page of logs** — call `get_org_logs` (`GET /orgLogs`) with the required
   `orgUrl` query parameter (must match the Console value exactly, including any trailing
   slash). Optionally filter with `service`, `logType`, `logSubtype`, and `tenant`, and set
   `limit` (1-1000, default 1000). With no `nextToken`, results start from one hour ago.
3. **Page forward** — follow the `next` URL from the response (it embeds `nextToken`) to
   retrieve the following page. Stop when `count` is less than your `limit`.
4. **Throttle** — wait at least five minutes between retrievals of log sets; back off as
   soon as a response returns fewer events than requested.

## Errors
Errors come back as plain JSON `{ "detail": "..." }` (not RFC 9457). Handle `401`
(invalid API authentication), `403` (not authorized for org), `422` (validation error),
and `500` (unknown error). See errors/cirrus-identity-problem-types.yml.
