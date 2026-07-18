# AIOps Bridge

The AIOps Bridge is the multi-source event ingestion layer that translates
governance events from all fifteen discipline sources into GovOps Observability
Store records and GPS Governance Events.

---

## Architecture

```
Discipline Sources                Bridge                    AIOps Layer
─────────────────   ──────────────────────────────   ──────────────────
projops (GitHub) → webhook-handler → ledger-client → Immutable Ledger
SecOps alerts    →                 → analysis-engine → Observability Store
FinGov telemetry →                 →                 → AO2 classification
NetworkGov feed  →                 →                 → AO3 anomaly queue
GPS Audit APIs   → gps-poller      →                 → AO4 EF updates
...              →                 →                 → AO5 GC buffer
```

The Bridge has three components:

**webhook-handler** — Receives incoming webhooks from discipline sources.
Validates the source against source-registry.json, verifies the webhook
signature (HMAC or post-quantum per source type), and enqueues the payload
for the analysis engine. Unregistered sources are rejected immediately
with a 403 response and a GPS `alert` of type `observer_gap`.

**analysis-engine** — Classifies incoming payloads, assigns Epistemic Field
regions, runs anomaly pre-scoring, and routes to consumers. The engine
maintains the 90-day rolling baselines used by AO3. In v0.1 this is
architecturally specified — the implementation hooks are stubbed in each
workflow step with `# In production:` comments.

**ledger-client** — Writes Governance Events to the configured Immutable
Ledger endpoint. Shared with projops. Implements GPS §4.5 append semantics
with at-least-once delivery and idempotency key per event.

---

## Extending projops

If you are running projops, the AIOps Bridge extends it rather than replacing
it. The projops Bridge writes Governance Events from GitHub initiative activity.
The AIOps Bridge adds:

- Multi-source ingestion (not just GitHub webhooks)
- GPS Audit Interface polling for non-webhook sources
- Analysis Engine classification layer
- Epistemic Field state management

In a combined deployment:
1. projops Bridge continues to write Governance Events from GitHub events
2. AIOps Bridge polls the projops Immutable Ledger chain via GPS §4.5
3. AIOps classifies projops events into the governance observability domain
4. AIOps feeds the GC measurement pipeline with governance domain data

---

## Adding a New Source

1. Register the source in `.github/govops/source-registry.json`
2. Add the source configuration to `aiops.yml` under `domains.<domain>.sources`
3. If the source uses webhooks: add the webhook secret as a repository secret
4. If the source uses GPS Audit Interface polling: add the audit API key as
   a repository secret
5. Commit and push — AO1 will begin ingesting on the next scheduled run

---

## Security

All webhook endpoints require signature verification before any payload is
processed. The verification method per source type:

| Source type | Verification method |
|---|---|
| `webhook` (default) | HMAC-SHA256 of payload body with `auth_secret` |
| `gps_audit` | GPS §7.1 post-quantum signature on all retrieved messages |
| `prometheus` | Bearer token in Authorization header |
| `custom` | Documented in source-specific collector README |

The AIOps Bridge will not process a payload that fails signature verification.
The failure is logged as a GPS `alert` of type `reality_integrity_failure`
severity `high` — a failed signature is a potential synthetic observation
attempt.
