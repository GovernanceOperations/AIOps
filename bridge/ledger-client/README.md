# Bridge — Ledger Client

Writes Governance Events to the Immutable Ledger. Shared architecture
with projops ledger-client — implementations may share the same library.

**Signing:** Every event signed with the deployment post-quantum private key
per `ledger.signature_algorithm` before transmission.

**Delivery:** At-least-once with exponential backoff (3 retries, max 30s).
Idempotency via `message_id` UUID v4.

**Chain integrity:** Each event carries `audit_trail.prior_hash`
(SHA-3-256 of prior event) per GPS §7.3.

**Self-observability path:** AO6 heartbeat uses a direct ledger-client call
bypassing the Analysis Engine. This path depends only on the ledger endpoint
URL, the signing key, and the prior hash — nothing else.
