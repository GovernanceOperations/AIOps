# GovOps AIOps Layer

**Repository:** GovernanceOperations/AIOps  
**Discipline:** Consolidated Observability and Intelligence (GOS §19)  
**Suite:** GovOps Suite Release 0.9-A  
**Specification:** GovOps AIOps Discipline Specification v0.1 (GADS)  
**Version:** 0.1  

---

## What This Is

The GovOps AIOps Layer is the sixteenth operational discipline in the GovOps
Suite. Where the [projops layer](https://github.com/GovernanceOperations/projops)
governs project delivery on GitHub, the AIOps layer provides the observability
substrate on which all fifteen other disciplines and all six Centre layers
depend.

It does three things:

**Observes.** Continuously ingests data from all seven observability domains
(technical, governance, financial, security, identity, stakeholder, adversarial)
across all connected discipline sources.

**Analyses.** Classifies incoming data, detects anomalies, runs cross-domain
correlation, and identifies synthetic observations.

**Produces.** Outputs Epistemic Field state, GC component measurements for
RiskOps, and anomaly alerts — all as GovOps Protocol GPS messages written to
the configured Immutable Ledger.

---

## Repository Structure

```
AIOps/
├── README.md                          This file
├── GOVERNANCE.md                      This repository's own governance record
├── CHANGELOG.md                       Version history
├── aiops.yml.example                  Fully commented reference configuration
├── .github/
│   ├── workflows/
│   │   ├── aiops-suite.yml            Composite caller — all seven processes
│   │   ├── ao1-continuous-ingestion.yml
│   │   ├── ao2-classification-routing.yml
│   │   ├── ao3-anomaly-detection.yml
│   │   ├── ao4-epistemic-field.yml
│   │   ├── ao5-gc-measurement.yml
│   │   ├── ao6-self-observability.yml
│   │   └── ao7-coverage-audit.yml
│   ├── ISSUE_TEMPLATE/
│   │   ├── observation-gap.md
│   │   ├── anomaly-review.md
│   │   └── coverage-audit-finding.md
│   └── govops/
│       └── source-registry.json       Registered observability sources
├── schemas/
│   ├── governance-event.json          GovOps Governance Event schema (from projops)
│   ├── observability-record.json      Observability Store record schema
│   ├── epistemic-field-update.json    Epistemic Field update schema
│   ├── gc-measurement.json            GC component measurement schema
│   └── aiops-config.json              aiops.yml configuration schema
├── bridge/
│   ├── README.md                      Bridge architecture guide
│   ├── app.yml                        GitHub App manifest (extends projops bridge)
│   ├── webhook-handler/               Multi-source event ingestion handler
│   ├── ledger-client/                 Immutable Ledger write client (shared with projops)
│   └── analysis-engine/               Anomaly detection and correlation engine
├── collectors/
│   ├── README.md                      Collector architecture guide
│   ├── technical/                     Technical domain collector config templates
│   ├── governance/                    Governance domain collector (GPS Audit Interface)
│   ├── financial/                     Financial domain collector config templates
│   ├── security/                      Security domain collector config templates
│   ├── identity/                      Identity domain collector config templates
│   ├── stakeholder/                   Stakeholder domain collector config templates
│   └── adversarial/                   Adversarial intelligence config templates
├── configs/
│   ├── anomaly-baselines.yml          Default baseline configuration per domain
│   ├── correlation-rules.yml          Cross-domain correlation rule definitions
│   └── retention-policy.yml           Observability Store retention policy
├── docs/
│   ├── vocabulary.md                  Plain-language term guide
│   ├── deploying-aiops.md             Administrator deployment guide
│   ├── adopting-aiops.md              How to connect discipline sources
│   ├── configuration-reference.md     aiops.yml field reference
│   ├── epistemic-field-guide.md       Epistemic Field operations guide
│   └── self-observability.md          Self-monitoring and failure recovery
└── templates/
    ├── OBSERVATION-GAP.md             Observation gap issue template
    ├── ANOMALY-REVIEW.md              Anomaly review issue template
    └── COVERAGE-AUDIT.md              Coverage audit finding template
```

---

## Quick Start

**Before Step 1 — this repository is safe to clone before you're ready to
configure it.** The workflows check for `aiops.yml` before doing anything
else. Until you complete Step 1, all seven AO processes are skipped (not
failed) on every scheduled run. See `docs/deploying-aiops.md#deployment-readiness-gate`
for details. If your Immutable Ledger does not exist yet, it is fine to
leave the repository in this state indefinitely — nothing will error.

**Otherwise, an easy drop in tarball should do the trick, just download and extract it.**

**Step 1 — Configure sources.**  
Copy `aiops.yml.example` to `aiops.yml` in this repository. Add your
Immutable Ledger endpoint, GPS endpoint (if using full protocol), and
the sources for each observability domain.

**Step 2 — Register sources.**  
Add each observability source to `.github/govops/source-registry.json`.
AIOps refuses data from unregistered sources.

**Step 3 — Install the Bridge.**  
Install the GovOps AIOps Bridge GitHub App (see `bridge/app.yml`) on the
organisations and repositories whose governance events AIOps should observe.

**Step 4 — Enable the workflows.**  
The `aiops-suite.yml` caller runs all seven AIOps processes. It is
triggered by schedule (hourly minimum in Normal mode), by incoming
webhook events, and by workflow_dispatch for manual runs.

**Step 5 — Connect projops.**  
If you are running projops, add your AIOps ledger endpoint to your
`govops.yml ledger.endpoint`. Projops Governance Events will flow through
the AIOps Bridge into the governance observability domain.

---

## Relationship to projops

projops and AIOps are complementary. projops governs what happens inside
a GitHub repository. AIOps observes what all fifteen disciplines produce
and feeds the Centre with measurement data.

| | projops | AIOps |
|---|---|---|
| GOS discipline | §17.11 Project Operations | §19 AIOps |
| Primary function | Gate and record | Observe and measure |
| Input | GitHub events | All discipline outputs |
| Output | Governance Events | GC measurements, anomaly alerts, EF state |
| Consumers | Memory Layer | RiskOps, Think Layer, Oversight Body |

---

## Known Gaps (v0.1)

1. **Analysis Engine implementation** — `bridge/analysis-engine/` contains the
   architecture and interfaces. The anomaly detection model implementations
   require operational baseline data before they can be calibrated. The engine
   produces outputs from day one using conservative defaults; accuracy improves
   as baseline data accumulates over 90 days.

2. **Adversarial Intelligence automation** — the adversarial domain collector
   configuration is provided. Automated adversarial simulation tooling must be
   provisioned separately and connected via the collector interface.

3. **Full GPS envelope** — AIOps produces complete Governance Events per GOS §6.6.
   Full GPS §1.2 message envelopes require Centre-side enrichment per GADS §9
   and GPS §8.2. This is declared as PDE-AO-001 (see GOVERNANCE.md).

---

## Specification

This repository implements the GovOps AIOps Discipline Specification v0.1
(GADS). The specification is the normative authority. Where this implementation
and GADS conflict, GADS governs.
