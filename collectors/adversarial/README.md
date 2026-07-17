# Adversarial Domain Collector

Collects adversarial intelligence outputs from SecOps (§17.2) and connects
external automated adversarial simulation tooling when available.

This domain differs from the security domain: security observes what has
happened; adversarial intelligence proactively discovers what could happen.

## Configuration Example

```yaml
domains:
  adversarial:
    sources:
      - id: "secops-threat-intel"
        type: "webhook"
        auth_secret: "SECOPS_WEBHOOK_SECRET"
        governance_critical: false
    collection_lag_alert_seconds: 3600
    collection_lag_critical_seconds: 86400
```

## Connecting Automated Adversarial Simulation

When automated adversarial simulation tooling is available (external to
this repository), connect it as a `custom` source type:

```yaml
      - id: "adversarial-sim"
        type: "custom"
        auth_secret: "ADVERSARIAL_SIM_SECRET"
        governance_critical: false
```

Implement the custom source authentication and payload normalisation
in `bridge/webhook-handler/`. The payload must conform to
`schemas/observability-record.json` with `domain: adversarial`.
