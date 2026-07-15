# Configuration Reference

Complete field reference for `aiops.yml`. All fields are optional unless
marked **REQUIRED**.

---

## `govops_version`

**REQUIRED.** The GOS version this deployment conforms to.

```yaml
govops_version: "0.9"
```

Currently only `"0.9"` is supported. Validated against the aiops-config.json schema.

---

## `aiops`

**REQUIRED block.**

### `aiops.deployment_id`

**REQUIRED.** Stable identifier for this AIOps deployment. Used as a prefix
in Immutable Ledger chain IDs and Governance Event payloads.

```yaml
aiops:
  deployment_id: "acme-corp-aiops"
```

Format: lowercase alphanumeric and hyphens. Minimum 3 characters.

### `aiops.tier`

**REQUIRED.** Operational tier: 1 (organisation-wide), 2 (division),
3 (programme), 4 (project). AIOps is typically tier 1.

---

## `ledger`

**REQUIRED block.**

### `ledger.endpoint`

**REQUIRED.** URL of the Immutable Ledger GPS §4.5 endpoint.

```yaml
ledger:
  endpoint: "https://your-ledger.example/govops/v1/audit/events"
```

### `ledger.auth_secret`

**REQUIRED.** Name of the GitHub Actions secret containing the Ledger API key.

### `ledger.chain_id`

**REQUIRED.** Immutable Ledger chain identifier for this AIOps deployment.
Format: `<deployment_id>-<environment>`. Example: `acme-corp-aiops-production`.

### `ledger.signature_algorithm`

**REQUIRED.** Post-quantum algorithm for Governance Event signing per GOS §11.2.
Supported values: `dilithium3` (default), `dilithium5`, `falcon512`, `falcon1024`.

### `ledger.alert_channel`

Optional. Webhook URL for alert notifications. Used when domain-specific
`alert_channel` is not set.

---

## `gps_endpoint`

Optional block. If `enabled: true`, AIOps wraps Governance Events in full
GPS §1.2 protocol messages and transmits to the Centre.

### `gps_endpoint.enabled`

Default: `false`. When false, AIOps writes directly to the Immutable Ledger
without GPS envelope wrapping (Centre-side enrichment path applies per PDE-AO-001).

### `gps_endpoint.url`

Required if `enabled: true`. URL of the Centre's GPS Decision Interface or
a compatible GPS relay.

### `gps_endpoint.auth_secret`

Name of the GitHub Actions secret containing the GPS API key.

### `gps_endpoint.session_negotiation`

Default: `true`. When true, AIOps performs GPS §3.2 HELLO/HELLO-ACK session
negotiation before transmitting messages.

---

## `domains`

Configuration for each of the seven observability domains. Each domain block
has the same structure.

### `domains.<domain>.enabled`

Default: `true`. Set to `false` to disable a domain entirely. A disabled
domain appears in the Unknown EF region and reduces EFCS.

### `domains.<domain>.sources[]`

Array of source configurations. Each source:

```yaml
sources:
  - id: "my-source"              # Unique identifier — must match source-registry.json
    type: "webhook"              # webhook | prometheus | gps_audit | custom
    endpoint: "https://..."      # Required for prometheus and gps_audit types
    auth_secret: "MY_SECRET"     # Name of GitHub Actions secret
    scrape_interval_seconds: 30  # For prometheus type only
    poll_interval_seconds: 60    # For gps_audit type only
    governance_critical: true    # Affects gap alert severity and OB escalation
```

**Source types:**
- `webhook` — receives HTTP POST from discipline sources. AIOps Bridge validates HMAC signature.
- `prometheus` — scrapes Prometheus-compatible metrics endpoint at `scrape_interval_seconds`.
- `gps_audit` — polls GPS §4.5 Audit Interface at `poll_interval_seconds`. Verifies GPS §7.1 signatures.
- `custom` — implement in Bridge webhook-handler extension.

### `domains.<domain>.collection_lag_alert_seconds`

How long without data before a `severity: high` `observer_gap` alert is produced.
Defaults per domain: security=30, governance=60, technical=60, financial=300,
identity=300, stakeholder=3600, adversarial=3600.

### `domains.<domain>.collection_lag_critical_seconds`

How long without data before a `severity: critical` alert and OB escalation.
Defaults: security=120, governance=300, technical=300, financial=900,
identity=900, stakeholder=86400, adversarial=86400.

---

## `analysis`

### `analysis.anomaly_detection`

#### `baseline_window_days`
Rolling window for baseline computation. Default: 90. Minimum: 7.

#### `baseline_recompute_hours`
How often baselines are recomputed. Default: 24 (daily).

#### `tiers`
Deviation thresholds for anomaly tiers (standard deviations from baseline mean).
```yaml
tiers:
  informational: 1.0
  notable: 2.0
  significant: 3.0
  critical: 5.0
```

#### `baseline_shift_alert_threshold`
Alert when baseline shifts by more than this proportion in 30 days (baseline
poisoning detection). Default: 0.20.

### `analysis.correlation`

#### `check_interval_minutes`
How often cross-domain correlation patterns are checked. Default: 5. Minimum: 1.

#### `correlation_window_minutes`
Sliding time window for pattern matching. Default: 60.

#### `mandatory_patterns`
Enable/disable each of the six mandatory GADS §4.3 patterns individually.
All default to `true`. Disabling a mandatory pattern is permitted operationally
but should be documented as a GMEPS §2.5 field report if done permanently.

### `analysis.synthetic_detection`

#### `enabled`
Default: `true`. Disabling synthetic detection is a governance risk — document
the reason and submit a GMEPS field report if done permanently.

#### `methods`
Enable/disable individual detection methods. All default to `true`.

---

## `epistemic_field`

### `confirmed_staleness_class1_days`
Days before a Class 1 instrument Confirmed item is demoted to Disputed.
Default: 365. Minimum: 30.

### `confirmed_staleness_class23_days`
Days before Class 2/3 instrument Confirmed items are demoted to Disputed.
Default: 180. Minimum: 14.

### `efcs_alert_threshold`
Alert when EFCS falls below this value. Default: 0.700.

### `efcs_critical_threshold`
Critical alert when EFCS falls below this value. Default: 0.500.

### `unknown_region_critical_domain_max_days`
Maximum days a governance-critical domain may remain in Unknown region without
an approved observation gap Decision Contract before OB escalation.
Default: 7. Per GADS §5.4.

---

## `self_monitoring`

### `heartbeat_interval_minutes`
How often AO6 writes a self-monitoring heartbeat. Default: 5.

### `heartbeat_alert_minutes`
Alert if no heartbeat within this interval. Default: 5.

### `heartbeat_critical_minutes`
Critical alert and OB escalation if no heartbeat within this interval. Default: 15.

### `latency_alert_seconds`
Alert if ingestion-to-classification lag exceeds this. Default: 30.

### `latency_critical_seconds`
Critical alert if lag exceeds this. Default: 60.

---

## `coverage_audit`

### `schedule`
Cron expression for weekly AO7 audit. Default: `0 9 * * 1` (Monday 09:00 UTC).

### `minimum_active_domains`
Minimum active domains for audit to pass. Default: 7 (all seven required).

### `alert_channel`
Webhook URL for coverage audit notifications. Falls back to `ledger.alert_channel`.

---

## `gc_measurement`

Cron schedules for AO5 GC measurement production per operating mode.
Schedules use standard cron syntax (GitHub Actions cron).

```yaml
gc_measurement:
  normal_mode_schedule: "0 8 * * 1"      # Weekly Monday
  stress_mode_schedule: "0 8 * * *"      # Daily
  crisis_mode_schedule: "0 */6 * * *"   # Every 6 hours
  graceful_degradation_schedule: "0 * * * *"  # Hourly
```

The operating mode is passed to AO5 by the aiops-suite.yml caller via
the `operating-mode` input. In production this input is set from the
current operating mode stored in the Observability Store.

---

## `retention`

Days to retain data in the Observability Store per record category.
Set to 0 for indefinite retention. See full policy in
`configs/retention-policy.yml`.

```yaml
retention:
  security_events_days: 2555      # 7 years
  technical_metrics_days: 730     # 2 years
  anomaly_records_days: 2555      # 7 years
  standard_telemetry_days: 90
```

Legal holds from LegalOps override all retention settings per
`configs/retention-policy.yml legal_hold` section.

---

## `runners`

### `runners.default`
GitHub Actions runner label. Default: `ubuntu-latest`. Change to a
self-hosted runner label if you require private network access to
source endpoints.
