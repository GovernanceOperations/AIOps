# AIOps Changelog

All version changes to the GovernanceOperations/AIOps repository are recorded here.
Each release entry cites the Governance Event recorded in the Immutable Ledger.

---

## v0.1 — 2026-06-01

**First release.**

**Governance Event reference:** GOVOPS-2026-0002-A10P5S19

### Added

- `README.md` — repository identity, structure, and quick start guide
- `GOVERNANCE.md` — initiative governance record with PDE-AO-001 declared
- `CHANGELOG.md` — this file
- `aiops.yml.example` — fully commented reference configuration for all seven
  observability domains, the analysis engine, Epistemic Field settings,
  self-monitoring, coverage audit, GC measurement schedules, and retention policy
- `.github/govops/source-registry.json` — eleven registered observability sources
  covering all seven domains and all fifteen GOS disciplines
- `.github/workflows/aiops-suite.yml` — composite caller for all seven processes;
  AO6 self-observability runs first unconditionally
- `.github/workflows/ao1-continuous-ingestion.yml` — source registry enforcement,
  GPS Audit Interface polling, provenance verification, gap detection
- `.github/workflows/ao2-classification-routing.yml` — schema validation, Epistemic
  Field region assignment, anomaly pre-scoring, cross-domain correlation matching,
  consumer routing
- `.github/workflows/ao3-anomaly-detection.yml` — per-domain anomaly detection,
  cross-domain correlation, synthetic observation detection, baseline poisoning check
- `.github/workflows/ao4-epistemic-field.yml` — Confirmed region extension,
  staleness management, Disagreement Condition processing, adapt step
  reclassifications, Unknown region identification, EFCS computation,
  persistent dispute escalation
- `.github/workflows/ao5-gc-measurement.yml` — all four GC component collections
  (F, CA, II, AT), GC and Confidence Factor computation, GPS evaluate message
  production, RiskOps transmission
- `.github/workflows/ao6-self-observability.yml` — heartbeat via direct path,
  pipeline health checks, EF currency check, domain coverage gaps, degradation alert
- `.github/workflows/ao7-coverage-audit.yml` — weekly discipline and domain
  coverage audit, EFCS check, heartbeat currency, gap issue creation
- `schemas/observability-record.json` — Observability Store record schema
- `schemas/epistemic-field-update.json` — Epistemic Field reclassification schema
- `schemas/gc-measurement.json` — GC component measurement output schema
- `schemas/aiops-config.json` — aiops.yml configuration schema
- `bridge/README.md` — bridge architecture and integration guide
- `bridge/app.yml` — GitHub App manifest
- `collectors/README.md` — collector architecture and domain configuration guide
- `configs/anomaly-baselines.yml` — default baseline configuration per domain
- `configs/correlation-rules.yml` — cross-domain correlation rule definitions
- `configs/retention-policy.yml` — Observability Store retention policy
- Issue templates for observation gaps, anomaly reviews, and coverage audit findings
- `docs/` — six documentation files covering deployment, adoption, configuration,
  Epistemic Field operations, and self-monitoring/failure recovery
- `templates/` — issue body templates for governance-significant events

### Known Gaps (v0.1)

1. **Analysis Engine implementation** — `bridge/analysis-engine/` specifies the
   architecture and interfaces. Anomaly detection model calibration requires
   90 days of operational baseline data. Conservative defaults apply until then.
2. **Adversarial Intelligence automation** — the adversarial domain collector
   is configured but external simulation tooling must be provisioned separately.
3. **Full GPS §1.2 envelope** — PDE-AO-001 declared in GOVERNANCE.md.
   Centre-dependent GPS fields require Centre-side enrichment per GADS §9.

### Specification Reference

Implements: GovOps AIOps Discipline Specification v0.1 (GADS)
Suite: GovOps Suite Release 0.9-A
