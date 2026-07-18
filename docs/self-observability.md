# Self-Observability and Failure Recovery

AIOps must be observable. A governance observability system that cannot
detect its own failures provides false assurance. This guide explains
how AO6 works and what to do when it detects a problem.

---

## The Self-Observability Channel

AO6 runs first in every aiops-suite.yml execution — before AO1, before
any other process. This ordering is unconditional: even if the primary
pipeline is degraded, AO6 heartbeat runs.

The heartbeat is written via a **direct path** to the Immutable Ledger —
it does not route through the analysis-engine Bridge component. This
structural independence means that a primary pipeline failure does not
simultaneously silence the self-monitoring signal.

The Oversight Body monitors the heartbeat independently by reading the
Immutable Ledger directly via GPS §4.5. If the OB detects that heartbeats
have stopped (or that AO6 is producing degradation alerts), it initiates
the recovery procedure below — without waiting for AIOps to self-recover.

---

## AO6 Health Checks

Each AO6 run performs five checks:

| Check | What it verifies | Alert on failure |
|---|---|---|
| Heartbeat | AO6 itself is running | severity: critical via direct path |
| Ingestion pipeline | AO1 last-run timestamp current | severity: critical |
| Analysis engine | AO2/AO3 classification current | severity: high |
| Epistemic Field | AO4 last-update current | severity: medium |
| Domain coverage | No domain has gone silent | severity: high |

---

## Failure Modes and Recovery

### Failure Mode 1 — Ingestion Pipeline Failure

**Symptom:** AO6 reports `ingestion_healthy: false`. AO1 last-run timestamp
is older than the expected interval. Records are accumulating in the input
queue without being processed.

**Safe state:** The Observability Store retains the last known good state.
The Epistemic Field reclassifies data from affected sources as Disputed
(unknown freshness). AIOps continues operating on non-failed domains.

**Recovery steps:**
1. Check the AO1 workflow run logs for errors
2. Verify source endpoints are reachable (check network, auth secrets)
3. Verify the source-registry.json is valid JSON
4. Trigger a manual AO1 run: `workflow_dispatch → process: ao1-ingestion`
5. If source is unreachable, create an observation-gap issue
6. Once AO1 is running again, AO4 will reclassify backlogged records

**GPS alert produced:** `alert_type: degraded_observation`, `severity: critical`

---

### Failure Mode 2 — Analysis Engine Failure

**Symptom:** AO6 reports `analysis_healthy: false`. AO1 is ingesting but
AO2/AO3 classification has stopped. Queue depth growing.

**Safe state:** Raw ingestion continues (data buffered). No new GC measurements
produced. RiskOps falls back to the most recent Measurement Record with
increased staleness penalty on Confidence Factor.

**Recovery steps:**
1. Check AO2 and AO3 workflow run logs
2. Verify the analysis-engine bridge component is operational
3. Check for schema validation failures in AO2 (malformed incoming records)
4. Trigger manual runs: `workflow_dispatch → process: ao2-classification`
   then `process: ao3-anomaly`
5. Once classification is running, process backlog in order: AO2 → AO3 → AO4

**GPS alert produced:** `alert_type: degraded_observation`, `severity: critical`

---

### Failure Mode 3 — Self-Observability Channel Failure

**Symptom:** The Oversight Body detects no heartbeat in the Immutable Ledger
for more than 15 minutes (heartbeat_critical_minutes threshold).

**This is the most serious failure mode.** AIOps is not producing any output —
neither primary pipeline nor self-monitoring. The Oversight Body initiates
recovery.

**Safe state:** The OB reads the Immutable Ledger directly. All fifteen
disciplines continue operating; their GPS messages still write to the ledger.
GC computation cannot proceed without AIOps inputs — RiskOps enters Graceful
Degradation Mode.

**Recovery steps (Oversight Body initiates):**
1. Read the Immutable Ledger directly to assess last known governance state
2. Notify the AIOps Lead and system administrators
3. Check GitHub Actions status — are workflows running at all?
4. Check repository secrets have not expired or been revoked
5. Trigger a manual AO6 run: `workflow_dispatch → process: ao6-self-monitoring`
6. If AO6 runs successfully, proceed with full pipeline restart:
   `workflow_dispatch → process: all`
7. If AO6 fails, escalate to infrastructure investigation
8. Once AIOps is restored, run AO7 coverage audit to assess what was missed

**GPS produced (when restored):** `alert_type: degraded_observation` severity
critical documenting the outage duration, followed by `record_type: verification`
confirming restoration.

---

## Safe State Principle

In all failure modes, AIOps follows one inviolable principle:

**It is better to expand the Disputed or Unknown region than to leave
Confirmed items that are no longer verifiable.**

When a data source fails, the Epistemic Field does not remain frozen at
its last known state. Items whose freshness can no longer be verified are
demoted to Disputed. This ensures that GC measurements produced after a
failure reflect genuine uncertainty rather than false confidence.

---

## Monitoring AIOps Health Externally

The Oversight Body (and any authorised operator) can assess AIOps health
independently by querying the Immutable Ledger:

```
GET /govops/v1/audit/events?chain_id=<deployment_id>-aiops-production
                            &operation_type=sense
                            &observation_type=aiops_heartbeat
                            &limit=1
```

The most recent heartbeat's timestamp should be within `heartbeat_critical_minutes`
of the current time. If it is not, AIOps is in Failure Mode 3.

To check overall pipeline health, look for recent `record` operations with
`record_type: verification` in the ledger — these are produced by AO6 at
the end of each run.
