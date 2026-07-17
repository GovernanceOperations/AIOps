# Identity Domain Collector

Extracts `actor_identity` fields from all GPS messages in the governance
domain. If you have a GPS Audit Interface source for the governance domain,
point the identity domain at the same endpoint — AIOps extracts actor_identity
automatically without a separate source.

## Configuration Example

```yaml
domains:
  identity:
    sources:
      - id: "gps-message-stream"
        type: "gps_audit"
        endpoint: "https://your-ledger.example/govops/v1/audit/events"
        auth_secret: "AIOPS_LEDGER_API_KEY"
        poll_interval_seconds: 60
        governance_critical: false
    collection_lag_alert_seconds: 300
    collection_lag_critical_seconds: 900
```

## What AIOps Extracts

- `actor_identity.actor_id` — for access pattern analysis
- `actor_identity.assurance_level` — for IAL verification monitoring
- Message timestamps — for access timing anomaly detection
- Operation type per actor — for privilege pattern analysis
