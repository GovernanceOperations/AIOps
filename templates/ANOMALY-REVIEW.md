# Anomaly Review — {{DOMAIN}} Domain

**Anomaly ID:** AR-{{YEAR}}-{{SEQ}}
**Detected:** {{TIMESTAMP}}
**Domain:** {{DOMAIN}}
**Tier:** {{TIER}}
**Deviation:** {{DEVIATION_SIGMA}}σ

## What Was Observed

**Baseline expectation:** {{BASELINE_DESCRIPTION}}
**Actual observation:** {{OBSERVED_VALUE}}
**Anomaly score:** {{ANOMALY_SCORE}}σ ({{TIER}} tier)

## Pattern Match

**Correlation pattern triggered:** {{PATTERN_ID_OR_NONE}}
**Domains involved:** {{DOMAINS}}

## Synthetic Observation Flags

- [ ] Statistical distribution
- [ ] Temporal sequence
- [ ] Cross-source consistency
- [ ] Provenance chain

**Verdict:** {{SYNTHETIC_VERDICT}}

## Governance Impact

| Item | Value |
|---|---|
| EF classification | {{EF_REGION}} |
| GC component | {{GC_COMPONENT}} |
| Estimated GC delta | {{GC_DELTA}} |
| Estimated GRL delta | {{GRL_DELTA}} |

## Investigation

**Root cause:** {{ROOT_CAUSE_OR_UNDER_INVESTIGATION}}

**Resolution type:**
- [ ] False positive — baseline needs calibration
- [ ] True anomaly — remediated
- [ ] True anomaly — accepted (Decision Contract ref: {{DC_REF}})
- [ ] Escalated to Oversight Body

**GMEPS entry:** {{ME_REF_OR_NONE}}

<!-- govops-schema: anomaly-review v0.9 -->
<!-- gps-alert-ref: {{GPS_ALERT_MESSAGE_ID}} -->
<!-- gps-record-ref: {{GPS_RECORD_MESSAGE_ID}} -->
