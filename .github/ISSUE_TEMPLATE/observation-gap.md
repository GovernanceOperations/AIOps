---
name: Observation Gap
about: Report a gap in AIOps observability coverage for a domain or discipline source
labels: aiops-observation-gap, aiops-governance
assignees: ''
---

<!-- govops-schema: observation-gap v0.9 -->

## Observation Gap Report

**Gap identifier:** OG-<!-- fill in: YYYY-NNN -->
**Detected by:** <!-- AO7 coverage audit | AO1 source gap | manual observation -->
**Detection timestamp:** <!-- ISO 8601 UTC -->

---

### Affected Domain

<!-- Select one:
- [ ] technical
- [ ] governance
- [ ] financial
- [ ] security
- [ ] identity
- [ ] stakeholder
- [ ] adversarial
-->

### Affected Discipline Source

**Discipline (GOS §):** <!-- e.g. SecOps §17.2 -->
**Source registry ID:** <!-- from .github/govops/source-registry.json -->
**Governance critical:** <!-- yes / no -->

### Gap Description

<!-- What observability data is missing?
What was the last successful collection timestamp for this source?
What is the expected collection interval? -->

**Last successful collection:** <!-- ISO 8601 or "never" -->
**Expected interval:** <!-- e.g. every 60 seconds / hourly / daily -->
**Gap duration:** <!-- how long has data been missing -->

### Governance Impact

**EF region affected:** <!-- confirmed / disputed / unknown -->
**GC component affected:** <!-- F / CA / II / AT / multiple / none -->
**Operating mode implication:** <!-- does this gap push the governance system toward Stress/Crisis? -->

**EFCS impact:** <!-- estimated reduction in EFCS from this gap -->

### Remediation Plan

<!-- Steps to restore this observability source -->
- [ ] Identify root cause of gap
- [ ] Restore source connection
- [ ] Verify data flowing to Observability Store
- [ ] Confirm source re-appears in AO7 coverage audit
- [ ] Update source-registry.json if source configuration has changed

**Target restoration date:** <!-- ISO 8601 -->
**Owner:** @<!-- GitHub username -->

### Governance Record

**GPS alert reference:** <!-- GPS alert message_id if produced by AO1/AO7 -->
**GMEPS register entry:** <!-- ME-YYYY-NNN if this gap indicates a spec/process gap -->

Per GADS §5.4: if this is a governance-critical domain and the gap exceeds 7 days
without an approved observation gap Decision Contract, GPS escalation to the
Oversight Body is required.

**Days until OB escalation threshold:** <!-- calculate from detection timestamp -->

---

**Resolution checklist:**
- [ ] Root cause identified and documented below
- [ ] Source restored and verified
- [ ] AO7 coverage audit confirms gap closed
- [ ] GPS record written to Immutable Ledger confirming resolution
- [ ] This issue closed with resolution summary
