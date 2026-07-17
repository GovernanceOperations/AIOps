# Financial Domain Collector

Collects financial telemetry from FinGov (§17.4). Receives GPS `sense`
messages (`observation_type: financial_telemetry`) and GPS `alert` messages
(over-authority events, anomalies, adverse variance).

## Configuration Example

```yaml
domains:
  financial:
    sources:
      - id: "fingov-telemetry"
        type: "webhook"
        auth_secret: "FINGOV_WEBHOOK_SECRET"
        governance_critical: false
    collection_lag_alert_seconds: 300
    collection_lag_critical_seconds: 900
```

## Primary GMF Instrument

FG-F-I1 (Financial Authority Compliance Score) is a Class 1 instrument
for the F component. Requires financial telemetry from this domain.
