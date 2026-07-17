# Governance Domain Collector

Collects GPS Governance Events from all fifteen disciplines. This is the
highest-value domain — every GPS message from every discipline belongs here.

| Source type | Best for |
|---|---|
| GPS Audit Interface poll | Any GPS-conformant implementation including projops |
| Webhook | PolicyOps compliance feed, exception events |

## Recommended Configuration

If all disciplines write to a shared Immutable Ledger, a single GPS Audit
Interface poll covers the entire governance domain:

```yaml
domains:
  governance:
    sources:
      - id: "govops-ledger"
        type: "gps_audit"
        endpoint: "https://your-ledger.example/govops/v1/audit/events"
        auth_secret: "AIOPS_LEDGER_API_KEY"
        poll_interval_seconds: 60
        governance_critical: true
```

## What AIOps Extracts

From each GPS Governance Event, AIOps classifies:
- Operation type → governance event category
- Actor identity → feeds identity domain
- Alert type → anomaly pre-scoring
- Governance context fields → GC measurement inputs
- Exception/bypass events → anomaly baseline comparison
