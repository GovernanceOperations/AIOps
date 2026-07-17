# Security Domain Collector

Collects security findings, incident events, gate results, and network
anomaly signals. This domain has the tightest collection lag thresholds
(alert: 30s, critical: 120s) — security events are time-critical.

| Discipline | GOS § | Expected signals |
|---|---|---|
| SecOps | §17.2 | SFR entries, SIR events, P1/P2 alerts |
| ISD | §17.5 | Gate pass/fail results, exception grants |
| NetworkGov | §17.9 | Network anomaly signals |

## Configuration Example

```yaml
domains:
  security:
    sources:
      - id: "secops-sfr"
        type: "webhook"
        auth_secret: "SECOPS_WEBHOOK_SECRET"
        governance_critical: true
      - id: "isd-gates"
        type: "webhook"
        auth_secret: "ISD_WEBHOOK_SECRET"
        governance_critical: false
      - id: "network-anomaly"
        type: "webhook"
        auth_secret: "NETWORK_WEBHOOK_SECRET"
        governance_critical: true
    collection_lag_alert_seconds: 30
    collection_lag_critical_seconds: 120
```

## Primary GMF Instrument

SO-II-I1 (Security Posture Score) is a Class 1 instrument for the II
component. It requires current data from this domain.
