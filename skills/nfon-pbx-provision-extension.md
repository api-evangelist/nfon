---
name: Provision a phone extension via the NFON PBX Configuration API
description: Authenticate with OAuth2 client credentials and list, create, and configure call-forwarding for a tenant's phone extensions.
api: openapi/nfon-pbx-configuration-api-openapi.json
base_url: https://api.nfon.net/configuration/v1
operations: [get_66, post_15, put_24]
---

# Provision a phone extension (NFON PBX Configuration API)

Base URL `https://api.nfon.net/configuration/v1`. Tenant-scoped under `/tenants/{participantExternalIdentifier}` where the identifier is the K-number (e.g. `K0611`).

## Rules
- Obtain an OAuth 2.0 Bearer token via the **client credentials** flow against `https://sso.cloud-cfg.com/`. Request a Client ID + Secret from integration@nfon.com. Send `Authorization: Bearer <access_token>`.
- The default permission is `tenant.manager`; some endpoints require additional permissions granted per service provider.
- No idempotency key is documented — avoid unconditional retries of POST writes.

## Steps
1. **List extensions** — `get_66` (GET /tenants/{participantExternalIdentifier}/targets/phone-extensions) to enumerate existing extensions for the tenant.
2. **Create an extension** — `post_15` (POST /tenants/{participantExternalIdentifier}/targets/phone-extensions) with the new extension payload.
3. **Configure call forwarding** — `put_24` (PUT /tenants/{participantExternalIdentifier}/targets/phone-extensions/{extensionNumber}/call-forwards) to set forwarding rules on the extension.

## Notes
- This API supersedes the legacy Service Portal API; path segments are unchanged, only the base URL and auth differ (see the migration guide).
- For bulk operations use the `.../phone-extensions/bulk` endpoints and poll async jobs via `GET /tenants/{tenant}/jobs/{eventId}`.
