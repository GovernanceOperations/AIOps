# Observation Gap — {{DOMAIN}} Domain

**Gap ID:** OG-{{YEAR}}-{{SEQ}}
**Detected:** {{TIMESTAMP}}
**Source:** {{SOURCE_ID}}
**Governance critical:** {{YES_NO}}

## What Is Missing

{{SOURCE_ID}} ({{DISCIPLINE}}, {{DOMAIN}} domain) has not produced data
since {{LAST_SEEN}}. Expected interval: {{EXPECTED_INTERVAL}}.

Gap duration at detection: {{GAP_DURATION}}.

## Governance Impact

| Item | Value |
|---|---|
| EF region affected | {{EF_REGION}} |
| GC component | {{GC_COMPONENT}} |
| EFCS before gap | {{EFCS_BEFORE}} |
| Estimated EFCS after | {{EFCS_AFTER}} |

## Required Actions

- [ ] Diagnose root cause (endpoint down / auth expired / source config changed)
- [ ] Restore source and verify data flowing
- [ ] Confirm AO7 shows domain coverage restored
- [ ] Write GPS record to ledger confirming resolution

**Owner:** @{{OWNER}}
**Target resolution:** {{TARGET_DATE}}

## OB Escalation

Per GADS §5.4: if this gap persists beyond 7 days (for governance-critical
domains), GPS escalation to Oversight Body is required.

**Escalation date if unresolved:** {{ESCALATION_DATE}}

<!-- govops-schema: observation-gap v0.9 -->
<!-- gps-alert-ref: {{GPS_ALERT_MESSAGE_ID}} -->
