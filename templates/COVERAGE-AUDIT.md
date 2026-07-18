# Coverage Audit — {{DATE}}

**Audit ID:** CA-{{YEAR}}-{{SEQ}}
**Run:** {{WORKFLOW_RUN_URL}}
**Result:** {{PASSED_OR_GAPS_FOUND}}

## Summary

| Check | Result |
|---|---|
| 15 disciplines with active sources | {{PASS_FAIL}} |
| 7 domains with active coverage | {{PASS_FAIL}} |
| All governance-critical sources active | {{PASS_FAIL}} |
| EFCS above operating mode minimum | {{PASS_FAIL}} ({{EFCS}}) |
| Self-monitoring heartbeat current | {{PASS_FAIL}} |

## Gaps Found

### Discipline Source Gaps

| Discipline | GOS § | Gap description |
|---|---|---|
| {{DISCIPLINE}} | {{GOS_SECTION}} | {{GAP_DESCRIPTION}} |

### Domain Coverage Gaps

| Domain | Status |
|---|---|
| {{DOMAIN}} | No active sources |

### Governance-Critical Sources Inactive

| Source ID | Days inactive |
|---|---|
| {{SOURCE_ID}} | {{DAYS}} |

## Required Actions

- [ ] {{ACTION_1}}
- [ ] {{ACTION_2}}

**Owner:** @{{OWNER}}
**Next audit:** {{NEXT_AUDIT_DATE}}

## OB Escalation

**Governance-critical domains in Unknown > 7 days:** {{LIST_OR_NONE}}
**Escalation required:** {{YES_NO}}
**GPS escalation ref:** {{MESSAGE_ID_OR_NA}}

<!-- govops-schema: coverage-audit v0.9 -->
<!-- gps-record-ref: {{GPS_RECORD_MESSAGE_ID}} -->
