# Epistemic Field Guide

The Epistemic Field (EF) is the governance system's continuously updated map
of what it knows, what is contested, and what it does not know. AIOps
maintains it. This guide explains how it works and what to do when items
move between regions.

---

## The Three Regions

**Confirmed** — Facts that have passed Multi-Observer Verification with no
Disagreement Condition. Each Confirmed item carries a Certification Hash and
an expiry date. When the expiry date passes without re-verification, the item
moves to Disputed automatically (AO4 staleness management).

**Disputed** — Facts where the Disagreement Condition is active — independent
observers have reached different conclusions. A Disputed item cannot support
Irreversible or Conditional governance decisions at full Observer Sufficiency.
Disputes are tracked with the specific observer positions and an OAS score.
Disputes persisting beyond 14 days trigger GPS escalation to the Oversight Body.

**Unknown** — Governance domains where AIOps has insufficient or no
observations. Unknown regions are not passive absences — they are active
governance gaps. Each Unknown region item produces a GPS `observer_gap` alert
and reduces the EFCS, which reduces the Confidence Factor, which reduces
Effective GC.

---

## EFCS — What It Measures

```
EFCS = Confirmed_inputs / (Confirmed + Disputed + Unknown)
```

EFCS measures how much of the governance system's knowledge is in the
Confirmed region. A higher EFCS means higher confidence in GC measurements.

EFCS alert thresholds (from `aiops.yml`):
- Alert: < 0.700
- Critical: < 0.500

Operating mode minimums:
- Normal mode: 0.700
- Stress mode: 0.600
- Crisis mode: 0.500
- Graceful Degradation: 0.300

When EFCS falls below the operating mode minimum, it contributes to a
Confidence Factor reduction that flows through to Effective GC.

---

## What Moves Items into Confirmed

1. **GPS `record` operations** with `input_verification_status: multi_observer_verified`
   and `agreement_assessment.disagreement_condition_active: false` — these extend
   the Confirmed region with the verified fact.

2. **GPS `adapt` operations** with `adapt_payload.epistemic_field_updates`
   containing `update_type: confirmed` or `update_type: disputed_resolution` —
   these are explicit Adapt step reclassifications.

3. **AO4 staleness re-verification** — when a stale item is re-verified
   (a new Multi-Observer Verification cycle completes successfully), AO4
   restores it to Confirmed with a new expiry date.

---

## What Moves Items into Disputed

1. **GPS `verify` operations** with `agreement_assessment.disagreement_condition_active: true`
   — these trigger reclassification of the affected facts from Confirmed to Disputed.

2. **AO3 anomaly detection** — when AO3 detects a contradiction between two
   sources reporting on the same fact, it flags the affected Confirmed items
   for Disputed review.

3. **AO4 staleness management** — when a Confirmed item passes its expiry date
   without re-verification, AO4 moves it to Disputed automatically and produces
   a re-verification request.

---

## What Moves Items into Unknown

Items enter the Unknown region when AIOps has no observations for a governance
domain within the expected collection lag window. Unknown is detected by AO4's
weekly Unknown region scan (and also by AO1 gap detection in real time).

---

## Resolving a Dispute

When a Disputed item appears in the AO4 output or in an anomaly review issue:

1. **Identify the disagreeing observers.** The Disputed item record includes
   `dispute_observer_positions` with each observer's position and OIS score.

2. **Determine whether the dispute is substantive.** Is it a genuine disagreement
   about a governance fact, or a data quality problem (stale data, measurement
   error)?

3. **For data quality disputes:** fix the underlying data source, re-run the
   verification cycle, and the item should return to Confirmed automatically
   when the new verification passes.

4. **For substantive disputes:** escalate to the Oversight Body. The OB reviews
   the observer positions, may commission additional observation, and determines
   the authoritative classification. This is a governed act requiring a GPS
   `approve` operation.

5. **Record the resolution** as a GPS `adapt` operation with
   `adapt_payload.epistemic_field_updates` containing `update_type: disputed_resolution`.
   AO4 processes this on the next run and moves the item to Confirmed.

---

## Closing an Unknown Region Gap

When AO4 or AO7 reports an Unknown region gap:

1. Identify the missing observability source for the affected domain.
2. Register and connect the source per `docs/adopting-aiops.md`.
3. Allow one AO1 cycle to ingest initial data.
4. Allow one AO4 cycle to classify the new data and move the domain items
   from Unknown to Confirmed (if data quality is sufficient) or Disputed
   (if only one observer).
5. Verify EFCS increases on the next AO5 measurement.

If the gap cannot be closed within 7 days for a governance-critical domain,
submit an observation gap Decision Contract to the Oversight Body per GADS §5.4
and create a coverage audit finding issue.

---

## The EF and GC Measurements

Every GC measurement produced by AO5 uses the current EFCS as the
`epistemic_coverage_component` of the Confidence Factor:

```
Confidence Factor = OAS × MU_factor × EFCS
Effective GC = Raw GC × Confidence Factor
```

A low EFCS directly reduces Effective GC even if the raw component scores
(F, CA, II, AT) are high. This is deliberate: a governance system that does
not know its own state cannot claim high governance capacity.

The EFCS value from AO4 is written into every GPS `evaluate` message via
`evaluate_payload.confidence_derivation.epistemic_coverage_component`
per AMD-001 §C.2.6.
