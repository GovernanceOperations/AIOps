---
name: Coverage Audit Finding
about: Document a finding from the weekly AO7 observability coverage audit
labels: aiops-coverage-gap, aiops-governance
assignees: ''
---

<!-- govops-schema: coverage-audit-finding v0.9 -->

## Coverage Audit Finding

**Audit identifier:** CA-<!-- fill in: YYYY-NNN -->
**Audit date:** <!-- ISO 8601 date -->
**Audit run:** <!-- link to AO7 workflow run -->
**Overall audit result:** <!-- passed | gaps found -->

---

### Coverage Summary

| Check | Result | Detail |
|---|---|---|
| All 15 disciplines have active sources | <!-- ✓ / ✗ --> | <!-- count or N/A --> |
| All 7 domains have active coverage | <!-- ✓ / ✗ --> | <!-- count or N/A --> |
| All governance-critical sources active | <!-- ✓ / ✗ --> | <!-- count inactive --> |
| EFCS above operating mode minimum | <!-- ✓ / ✗ --> | <!-- EFCS value --> |
| Self-monitoring heartbeat current | <!-- ✓ / ✗ --> | <!-- minutes since last --> |

---

### Discipline Coverage Gaps

<!-- List each discipline with no active registered source -->

| Discipline | GOS § | Missing domain(s) | Days without coverage |
|---|---|---|---|
| | | | |

### Domain Coverage Gaps

<!-- List each domain with no active source -->

| Domain | Governance critical sources | Status |
|---|---|---|
| | | |

### Governance-Critical Sources Inactive

<!-- List each governance-critical source that is not active -->

| Source ID | Discipline | Domain | Last active | Status |
|---|---|---|---|---|
| | | | | |

---

### Epistemic Field Status

**Current EFCS:** <!-- decimal 0.000–1.000 -->
**Operating mode minimum:** <!-- 0.700 (normal) / 0.600 (stress) / 0.500 (crisis) -->
**EFCS status:** <!-- above minimum / BELOW MINIMUM -->

**Confirmed region items:** <!-- count -->
**Disputed region items:** <!-- count -->
**Unknown region items:** <!-- count -->

---

### Required Actions

<!-- One action item per gap found -->

**Discipline source gaps:**
- [ ] Register source for `<discipline>` in `.github/govops/source-registry.json`
- [ ] Add source configuration to `aiops.yml` under `domains.<domain>.sources`

**Domain coverage gaps:**
- [ ] Connect at least one active source for `<domain>` domain
- [ ] Verify source type and authentication in `aiops.yml`

**Governance-critical source gaps:**
- [ ] Reactivate `<source_id>` — check endpoint availability and authentication

**EFCS below minimum:**
- [ ] Review Disputed and Unknown region items in AO4 output
- [ ] Prioritise re-verification of stale Confirmed items
- [ ] Submit observation gap Decision Contract to Oversight Body if
      any governance-critical domain Unknown > 7 days

---

### OB Escalation Assessment

Per GADS §5.4: if any governance-critical domain has been in Unknown EF region
for more than 7 days, GPS escalation to Oversight Body is required.

**Governance-critical domains in Unknown region > 7 days:**
<!-- list or "none" -->

**OB escalation required:** <!-- yes / no -->
**GPS escalation message_id:** <!-- if escalated -->

---

### Governance Record

**GPS record message_id:** <!-- audit record written to Immutable Ledger by AO7 -->
**Next audit scheduled:** <!-- ISO 8601 date — next Monday 09:00 UTC -->

---

**Resolution checklist:**
- [ ] All gaps above assigned to owners
- [ ] Sources restored and verified in source-registry.json
- [ ] Next AO7 run confirms all checks passing
- [ ] GPS record written to Immutable Ledger confirming resolution
- [ ] This issue closed with next passing audit run linked
