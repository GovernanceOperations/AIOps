# Bridge — Webhook Handler

Receives incoming HTTP POST events from discipline sources, validates
signatures, and enqueues payloads for the Analysis Engine.

## Interface

**Endpoint:** `POST /webhook`

**Authentication:** HMAC-SHA256 on raw request body using the `auth_secret`
per source in `aiops.yml`. Provided in `X-Hub-Signature-256` header
(GitHub-compatible) or `X-GovOps-Signature` header.

**Unregistered source rejection:** Any source not in `source-registry.json`
with `status: active` → HTTP 403 + GPS `alert` of type `observer_gap`.

**Payload normalisation:** Normalises source-specific formats to
`observability-record.json` before passing to the Analysis Engine.

## Implementation (v0.1)

Deploy as a separate service with `bridge/app.yml` providing event routing.
Must satisfy: signature verification → source registry lookup → payload
normalisation → enqueue → HTTP 202 (success), 403 (auth fail), 400 (schema fail).
