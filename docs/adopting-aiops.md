# Adopting AIOps — Connecting Discipline Sources

Guide for connecting each of the fifteen GOS disciplines as observability
sources to AIOps.

---

## Connection Patterns

AIOps supports three source connection patterns:

**Webhook push** — The discipline system pushes events to the AIOps Bridge
webhook endpoint. Best for disciplines that already produce GPS alert/record
messages (SecOps, FinGov, PolicyOps, SocietalOps, ISD, NetworkGov).

**GPS Audit Interface poll** — AIOps polls the discipline's GPS §4.5 Audit
Interface at a configured interval. Best for disciplines with an Immutable
Ledger endpoint (ProjOps, any GPS-conformant implementation).

**Metrics scrape** — AIOps scrapes metrics from a Prometheus-compatible
endpoint. Best for technical domain sources (ITSM availability, infrastructure
metrics, DORA metrics from CD pipelines).

---

## Domain Connection Guide

### Technical Domain (§17.1 CD, §17.3 InfraConfig, §17.8 PlatformOps, §17.9 NetworkGov, §17.10 ITSM)

**What AIOps needs from this domain:**
- Infrastructure availability metrics (ITSM §17.10 — CMDB CI availability)
- Pipeline run events (CD §17.1 — DORA metrics, gate results)
- Reconciliation state changes (InfraConfig §17.3 — drift events)
- Platform service metrics (PlatformOps §17.8 — SLA metrics)
- Network anomaly signals (NetworkGov §17.9 — already flowing to SecOps)

**Recommended connection:**

```yaml
# aiops.yml
domains:
  technical:
    sources:
      # ITSM — governance-critical CI availability via Prometheus
      - id: "itsm-availability"
        type: "prometheus"
        endpoint: "https://your-prometheus.example"
        auth_secret: "PROMETHEUS_API_KEY"
        scrape_interval_seconds: 30
        governance_critical: true
      # InfraConfig — reconciliation webhook
      - id: "infra-reconciliation"
        type: "webhook"
        auth_secret: "INFRA_WEBHOOK_SECRET"
        governance_critical: true
```

**Webhook payload format for InfraConfig:**
The InfraConfig reconciliation webhook should POST a GPS `sense` message
with `observation_type: "infrastructure_state"` to the AIOps Bridge endpoint.

---

### Governance Domain (all disciplines via GPS messages, §18.3 PolicyOps, §17.11 ProjOps)

**What AIOps needs:** GPS Governance Events from all disciplines. This is
the highest-value domain — every GPS message from every discipline belongs
here.

**Recommended connection:**

```yaml
domains:
  governance:
    sources:
      # Poll the shared Immutable Ledger for all GPS Governance Events
      - id: "govops-ledger"
        type: "gps_audit"
        endpoint: "https://your-ledger.example/govops/v1/audit/events"
        auth_secret: "AIOPS_LEDGER_API_KEY"
        poll_interval_seconds: 60
        governance_critical: true
      # PolicyOps compliance feed webhook
      - id: "policy-engine"
        type: "webhook"
        auth_secret: "POLICY_WEBHOOK_SECRET"
        governance_critical: false
```

If all fifteen disciplines write to the same Immutable Ledger, a single
GPS Audit Interface source covers the entire governance domain.

---

### Financial Domain (§17.4 FinGov)

**What AIOps needs:** FinGov GPS `sense` messages (financial telemetry),
GPS `alert` messages (over-authority, anomaly, variance).

```yaml
domains:
  financial:
    sources:
      - id: "fingov-telemetry"
        type: "webhook"
        auth_secret: "FINGOV_WEBHOOK_SECRET"
        governance_critical: false
    collection_lag_alert_seconds: 300
```

---

### Security Domain (§17.2 SecOps, §17.5 ISD, §17.9 NetworkGov)

**What AIOps needs:** SFR entries, SIR events, ISD gate results, network
anomaly signals. This domain has the tightest collection lag (30s alert).

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

---

### Identity Domain (all disciplines)

**What AIOps needs:** `actor_identity` fields extracted from all GPS messages.
If you have a single GPS Audit Interface source for the governance domain,
point the identity domain at the same endpoint — AIOps will extract
actor_identity fields automatically.

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
```

---

### Stakeholder Domain (§18.4 SocietalOps)

**What AIOps needs:** SocietalOps GPS `sense` messages (feedback signals,
retaliation indicators), disclosure events. Stakeholder signals are
slower-moving — collection lag alerts are set in hours not seconds.

```yaml
domains:
  stakeholder:
    sources:
      - id: "societal-ops-feedback"
        type: "webhook"
        auth_secret: "SOCIETAL_WEBHOOK_SECRET"
        governance_critical: false
    collection_lag_alert_seconds: 3600
    collection_lag_critical_seconds: 86400
```

---

### Adversarial Domain (§17.2 SecOps adversarial intelligence)

**What AIOps needs:** SecOps adversarial intelligence outputs, threat
intelligence signals. External automated adversarial simulation tools
connect here when available.

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

For automated adversarial simulation tooling: connect via a `custom` source
type and implement the authentication and payload format in your Bridge
webhook handler extension.

---

## Testing a Source Connection

After registering a source and configuring the webhook or poll endpoint:

1. Trigger a manual AO1 run:
   `Actions → AIOps Observability Suite → Run workflow → Process: ao1-ingestion`

2. Check the AO1 output for:
   - Source registered: ✓ (listed in source registry check)
   - No gap alert for this source (no `observer_gap` alert produced)
   - Records ingested: count > 0 for this source

3. Run AO7 to confirm domain coverage:
   `Actions → AIOps Observability Suite → Run workflow → Process: ao7-coverage-audit`

4. Verify the domain shows ✓ in the AO7 output.
