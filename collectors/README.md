# AIOps Collectors

Collectors are the domain-specific configuration templates that tell the
AIOps Bridge how to collect observability data from each of the seven domains.

Each subdirectory contains:
- A `README.md` explaining the domain and what to configure
- Example configuration snippets for common source types
- A mapping table showing which GOS disciplines feed this domain

---

## Domain Overview

| Domain | Primary disciplines | Collection method |
|---|---|---|
| `technical/` | ITSM §17.10, InfraConfig §17.3, CD §17.1, PlatformOps §17.8, NetworkGov §17.9 | Prometheus metrics, webhooks, GPS Audit polling |
| `governance/` | All disciplines via GPS messages, PolicyOps §18.3, ProjOps §17.11 | GPS Audit Interface polling, webhooks |
| `financial/` | FinGov §17.4 | Webhooks (GPS sense messages) |
| `security/` | SecOps §17.2, ISD §17.5, NetworkGov §17.9 | Webhooks (GPS alert/record messages) |
| `identity/` | All disciplines (actor_identity extraction from GPS messages) | GPS Audit Interface polling |
| `stakeholder/` | SocietalOps §18.4 | Webhooks (GPS sense messages) |
| `adversarial/` | SecOps §17.2 (adversarial intelligence output) | Webhooks, external threat intel APIs |

---

## Adding a Collector

1. Choose the appropriate domain subdirectory
2. Copy the example configuration to `aiops.yml` under `domains.<domain>.sources`
3. Register the source in `.github/govops/source-registry.json`
4. Add required secrets to the repository secret store
5. Test with a `workflow_dispatch` trigger to `aiops-suite.yml` with
   `process: ao1-ingestion`

---

## Collector Security Requirements

All collectors must satisfy two security requirements before AIOps processes
their data:

**Registration.** The source must appear in `source-registry.json` with
`status: active`. AIOps refuses data from unregistered sources — this is
not configurable. An unregistered source attempting to deliver data
produces a GPS `alert` of type `observer_gap`.

**Authentication.** The source must authenticate per its type:
- Webhooks: HMAC-SHA256 signature on the payload body
- GPS Audit: post-quantum signed GPS messages per GPS §7.1
- Prometheus: Bearer token

These requirements are enforced in AO1 before any payload reaches AO2
for classification.

---

## Observability Store Retention

Data from each collector is retained per the policy in `configs/retention-policy.yml`.
Collectors that produce Governance Events (e.g., GPS message sources) write
those events to the Immutable Ledger (indefinite retention) in addition to
the Observability Store (time-bounded retention).

Security event data is retained for 7 years minimum per GADS §3.2.
Standard telemetry is retained for 90 days by default.
