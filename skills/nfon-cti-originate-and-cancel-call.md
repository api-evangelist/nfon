---
name: Originate and cancel a call with the NFON CTI API
description: Authenticate to the NFON CTI API, look up a tenant's extensions, start an outgoing call, and cancel it by UUID.
api: openapi/nfon-cti-api-openapi.yml
base_url: https://providersupportdata.cloud-cfg.com/v1
operations: [createToken, getPhoneExtensionData, originate, cancel]
---

# Originate and cancel a call (NFON CTI API)

Server-to-server call control. Base URL `https://providersupportdata.cloud-cfg.com/v1`.

## Rules
- Send `Authorization: Bearer <access_token>` on every call except `createToken`.
- Send a `User-Agent` header of the form `<productname>/<productversion> (<K-Account>)` on **every** request.
- Access tokens expire after **5 minutes** — refresh with `refreshAccessToken` (PUT /login) or re-`createToken`.
- Errors return `{"error": "<message>"}`; there is no idempotency key, so do not blindly retry `originate`.

## Steps
1. **Authenticate** — `createToken` (POST /login) with `{ "username": "<API_USERNAME>", "password": "<API_PASSWORD>" }`. Store the returned access + refresh tokens.
2. **Discover extensions** — `getPhoneExtensionData` (GET /extensions/phone/data) to get the tenant's extension numbers and names; pick the A-number (caller) extension.
3. **Start the call** — `originate` (POST /extensions/phone/calls) with the A-number and B-number (callee). The API dials the A-number first, then connects to the B-number. Capture the returned call **UUID**.
4. **End the call** — `cancel` (DELETE /extensions/phone/calls/{uuid}) with the UUID from step 3.

## Notes
- Only incoming direct-to-extension calls are officially supported; group/skill/queue/forwarded/conference scenarios are out of scope.
- For live monitoring, use `stream` (GET /extensions/phone/calls) which pushes new call events over Server-Sent Events.
