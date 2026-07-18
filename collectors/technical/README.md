# Technical Domain Collector

Collects availability metrics, pipeline events, reconciliation state, and
platform service metrics from the Technical domain disciplines:

| Discipline | GOS § | Expected signals |
|---|---|---|
| ITSM | §17.10 | CI availability (Prometheus), incident events |
| CD | §17.1 | Pipeline run events, gate results, DORA metrics |
| InfraConfig | §17.3 | Reconciliation state changes, drift events |
| PlatformOps | §17.8 | Platform service metrics, provisioning events |
| NetworkGov | §17.9 | Network anomaly signals (also feeds security domain) |

## Recommended Source Types

- **Prometheus scrape** — for ITSM availability metrics and CD DORA metrics
- **Webhook** — for InfraConfig reconciliation events and PlatformOps events

## Configuration Example

```yaml
# aiops.yml
domains:
  technical:
    sources:
      - id: "itsm-availability"
        type: "prometheus"
        endpoint: "https://your-prometheus.example"
        auth_secret: "PROMETHEUS_API_KEY"
        scrape_interval_seconds: 30
        governance_critical: true
      - id: "infra-reconciliation"
        type: "webhook"
        auth_secret: "INFRA_WEBHOOK_SECRET"
        governance_critical: true
```

## Governance-Critical Sources

Sources marked `governance_critical: true` use tighter collection lag
thresholds (alert: 60s, critical: 300s). Governance-critical CI availability
is the primary input to the ITSM-AT-I1 GMF instrument.
