# Stakeholder Domain Collector

Collects stakeholder welfare signals from SocietalOps (§18.4). Receives
GPS `sense` messages (`observation_type: stakeholder_feedback`), retaliation
indicator alerts, and disclosure events.

Stakeholder signals are slower-moving than security or technical signals.
Collection lag thresholds are set in hours, not seconds.

## Configuration Example

```yaml
domains:
  stakeholder:
    sources:
      - id: "societal-ops-feedback"
        type: "webhook"
        auth_secret: "SOCIETAL_WEBHOOK_SECRET"
        governance_critical: false
    collection_lag_alert_seconds: 3600       # 1 hour
    collection_lag_critical_seconds: 86400  # 24 hours
```

## Primary GMF Instrument

SO-F-I1 (Stakeholder Feedback Fidelity Score) is a Class 1 instrument
for the F component. It is the most structurally independent Fidelity
instrument in the suite — stakeholder feedback comes from outside the
Governing Entity.
