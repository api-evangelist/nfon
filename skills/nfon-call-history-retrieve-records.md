---
name: Retrieve and stream NFON call history records
description: Authenticate with Login with NFON (OAuth2) and retrieve, filter, or stream call history records including AI transcriptions and summaries.
api: openapi/nfon-call-history-api-openapi.yml
base_url: https://api.nfon.com/call-history
operations: ["GET /records", "GET /records/{uuid}"]
---

# Retrieve and stream call history records (NFON Call History API)

Base URL `https://api.nfon.com/call-history`. Early access under **Login with NFON**.

## Rules
- Obtain an OAuth 2.0 access token via the Authorization Code + PKCE flow against the NFON Identity Provider (`https://sso.cloud-cfg.com/`); partners receive their own OAuth client. Send `Authorization: Bearer <access_token>`.
- Send a `User-Agent` header `<productname>/<productversion>` on every request.
- The API is early access and may change on short notice — subscribe to "API Breaking Changes" on https://status.nfon.com.

## Steps
1. **List records** — `GET /records`. Filter with query params: `search`, `filter` (e.g. `status:missed`, `direction:inbound`, `transcription`, `internal:<ext>`, `global:<num>`), `from`/`to` timestamps, `limit`, `offset`, `sort=asc|desc`, `complete=true` (to include full transcripts/summaries).
2. **Stream records** — repeat `GET /records` with `Accept: text/event-stream` to receive records as Server-Sent Events. A record is **re-sent with the same `uuid`** when it changes (e.g. transcription finishes); de-duplicate/replace by `uuid`.
3. **Get one record** — `GET /records/{uuid}` for full detail of a single call, including transcript entries and summary when available.

## Notes
- Deletion is available via `DELETE /records` (bulk, by filter) and `DELETE /records/{uuid}` (single) — irreversible.
- Number `context` values (`global`, `<K-number>`, `emergency`, `short`, `conference`) and `status`/`destination_type` enums may grow; treat unknown values per the spec's forward-compatibility rules.
