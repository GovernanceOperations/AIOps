---
name: Anomaly Review
about: Document and investigate an anomaly detected by AO3 anomaly detection
labels: aiops-anomaly, aiops-governance
assignees: ''
---

<!-- govops-schema: anomaly-review v0.9 -->

## Anomaly Review

**Anomaly identifier:** AR-<!-- fill in: YYYY-NNN -->
**Detected by:** <!-- AO3 anomaly detection | AO3 cross-domain correlation | manual -->
**Detection timestamp:** <!-- ISO 8601 UTC -->
**Anomaly tier:** <!-- informational | notable | significant | critical -->

---

### Anomaly Classification

**Domain(s):** <!-- technical | governance | financial | security | identity | stakeholder | adversarial -->
**Alert type:** <!-- metric_drift | reality_integrity_failure | observer_gap | governance_bypass | degraded_observation -->
**GPS alert message_id:** <!-- if produced by AO3 -->
**Deviation score:** <!-- standard deviations from domain baseline -->

### Correlation Pattern (if applicable)

**Pattern triggered:** <!-- CORR-001 through CORR-006 or none -->
**Correlation domains:** <!-- domains involved in the pattern -->
**Pattern window:** <!-- time window in which the pattern was detected -->

### Synthetic Observation Flags (if applicable)

<!-- Check any synthetic observation detection flags raised by AO3 -->
- [ ] Statistical distribution anomaly
- [ ] Temporal sequence anomaly
- [ ] Cross-source inconsistency
- [ ] Provenance chain anomaly

**Synthetic observation verdict:** <!-- confirmed synthetic | inconclusive | ruled out -->

### Description

<!-- What was observed? Describe the anomaly in plain terms.
What is the baseline expectation and what was actually observed?
Which observability records triggered the anomaly detection? -->

**Baseline expectation:**
**Observed value/pattern:**
**Observability record IDs:** <!-- from Observability Store -->

### Governance Impact Assessment

**Epistemic Field implication:**
- Current EF classification: <!-- confirmed / disputed / unknown -->
- Should classification change? <!-- yes / no, and why -->

**GC component impact:**
- Component affected: <!-- F / CA / II / AT / multiple / none -->
- Estimated GC delta: <!-- + or - decimal -->
- GRL delta: <!-- + decimal if risk increasing -->

**Operating mode consideration:**
- Current mode: <!-- normal / stress / crisis / graceful_degradation -->
- Does this anomaly push toward next mode? <!-- yes / no -->

### Investigation Findings

<!-- Document what was found during investigation -->

**Root cause:** <!-- determined / under investigation / unknown -->

**Root cause description:**

**Disciplines involved:**
- [ ] CD §17.1
- [ ] SecOps §17.2
- [ ] InfraConfig §17.3
- [ ] FinGov §17.4
- [ ] ISD §17.5
- [ ] AI Governance §17.6
- [ ] DataOps §17.7
- [ ] PlatformOps §17.8
- [ ] NetworkGov §17.9
- [ ] ITSM §17.10
- [ ] ProjOps §17.11
- [ ] LegalOps §18.1
- [ ] RiskOps §18.2
- [ ] PolicyOps §18.3
- [ ] SocietalOps §18.4
- [ ] AIOps §19

### Resolution

**Resolution type:**
- [ ] False positive — baseline needs calibration
- [ ] True anomaly — root cause found and remediated
- [ ] True anomaly — accepted as operational variance (Decision Contract required)
- [ ] Escalated to Oversight Body
- [ ] Under investigation

**Remediation actions taken:**

**Baseline update required?** <!-- yes / no — if yes, describe the calibration change -->

**GMEPS submission required?**
<!-- If this anomaly revealed a specification or process gap, submit to
the Meta-Evolution Register per GADS §13 Decision Contract Requirements
and GMEPS §2.5. -->
- [ ] No specification gap identified
- [ ] GMEPS register entry created: ME-<!-- YYYY-NNN -->

### Governance Records

**GPS alert message_id:** <!-- from AO3 -->
**GPS escalation message_id:** <!-- if escalated to OB -->
**GPS record message_id:** <!-- resolution record written to ledger -->

---

**Resolution checklist:**
- [ ] Anomaly investigated and root cause determined (or accepted as unknown)
- [ ] EF classification updated if required (via AO4)
- [ ] GC measurement rerun if GC was materially affected
- [ ] Baseline calibration updated if false positive
- [ ] GPS record written to Immutable Ledger confirming resolution
- [ ] GMEPS entry submitted if spec/process gap found
- [ ] This issue closed with resolution classification above
