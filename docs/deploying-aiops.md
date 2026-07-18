# Deploying AIOps

Administrator guide for deploying the GovOps AIOps Layer at
`GovernanceOperations/AIOps`.

---

## Prerequisites

Before deploying AIOps, ensure you have:

1. **An Immutable Ledger endpoint** satisfying GPS §4.5 append-only requirements.
   If you are running projops, you already have one. AIOps can share the same
   Immutable Ledger — it uses a separate chain ID (`<deployment_id>-aiops-production`).

2. **Post-quantum signing keys** per GOS §11.2. AIOps signs every Governance
   Event it writes. In v0.1 this uses Dilithium3 by default. Your Immutable
   Ledger must verify these signatures. If you are using the same ledger as
   projops, the key infrastructure is already in place — add an AIOps-specific
   key pair to the keystore.

3. **At least one configured observability source** per domain you want covered.
   See `docs/adopting-aiops.md` for source connection instructions.

4. **Repository secrets** configured in `GovernanceOperations/AIOps` settings.
   Minimum required secrets are listed in Step 4 below.

---

## Step 1 — Fork or clone the repository

```bash
gh repo clone GovernanceOperations/AIOps
cd AIOps
```

Or use the repository as a template by creating a new repository and
pushing the AIOps content into it.

---

## Step 2 — Configure aiops.yml

Copy the example configuration and fill in your values:

```bash
cp aiops.yml.example aiops.yml
```

At minimum, complete these fields before any workflow will run:

```yaml
govops_version: "0.9"

aiops:
  deployment_id: "your-org-aiops"    # REQUIRED
  tier: 1                             # REQUIRED

ledger:
  endpoint: "https://..."             # REQUIRED — your Immutable Ledger
  auth_secret: "AIOPS_LEDGER_API_KEY" # REQUIRED — name of GitHub secret
  chain_id: "your-org-aiops-prod"    # REQUIRED
  signature_algorithm: "dilithium3"   # REQUIRED
```

Enable the domains you have sources for. Domains with no configured sources
will appear as Unknown in the Epistemic Field and reduce EFCS.

Commit `aiops.yml` to the repository:

```bash
git add aiops.yml
git commit -m "Configure AIOps deployment"
git push
```

---

## Step 3 — Register your observability sources

Edit `.github/govops/source-registry.json` to add or update your sources.
The default registry contains eleven example sources — update `source_id`,
`discipline`, and `status` to match your actual environment.

A source with `status: active` is expected to produce data. A source that
is registered but not yet producing data will trigger observation gap alerts.
Set `status: pending` for sources you plan to connect but have not yet
configured.

```json
{
  "source_id": "your-secops-sfr",
  "source_name": "SecOps SFR Feed",
  "domain": "security",
  "type": "webhook",
  "discipline": "SecOps",
  "gos_section": "§17.2",
  "governance_critical": true,
  "registered_date": "2026-06-01",
  "status": "active"
}
```

Commit the updated registry:

```bash
git add .github/govops/source-registry.json
git commit -m "Register observability sources"
git push
```

---

## Step 4 — Configure repository secrets

In your GitHub repository settings (`Settings → Secrets and variables → Actions`),
add these secrets:

| Secret name | Required | Description |
|---|---|---|
| `AIOPS_LEDGER_API_KEY` | Yes | API key for the Immutable Ledger endpoint |
| `AIOPS_GPS_API_KEY` | Only if GPS endpoint enabled | API key for the Centre GPS endpoint |
| `PROJOPS_AUDIT_KEY` | If using projops source | API key for projops ledger GPS §4.5 audit |
| `PROMETHEUS_API_KEY` | If using Prometheus source | Bearer token for Prometheus endpoint |
| `SECOPS_WEBHOOK_SECRET` | If using SecOps webhook source | HMAC secret for SecOps webhook |
| `ISD_WEBHOOK_SECRET` | If using ISD webhook source | HMAC secret for ISD gate result webhook |
| `NETWORK_WEBHOOK_SECRET` | If using NetworkGov webhook | HMAC secret for NetworkGov webhook |
| `SOCIETAL_WEBHOOK_SECRET` | If using SocietalOps webhook | HMAC secret for SocietalOps webhook |
| `FINGOV_WEBHOOK_SECRET` | If using FinGov webhook | HMAC secret for FinGov webhook |
| `INFRA_WEBHOOK_SECRET` | If using InfraConfig webhook | HMAC secret for InfraConfig webhook |
| `POLICY_WEBHOOK_SECRET` | If using PolicyOps webhook | HMAC secret for PolicyOps webhook |

For each webhook-type source in your `aiops.yml`, a corresponding secret
must exist with the name you specified in the `auth_secret` field.

---

## Step 5 — Install the Bridge GitHub App

The AIOps Bridge GitHub App enables AIOps to receive webhook events from
GitHub-hosted governance sources (including projops).

1. Create a new GitHub App from the manifest in `bridge/app.yml`:

```bash
# In GitHub: Settings → Developer Settings → GitHub Apps → New GitHub App
# Or via the API:
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  /app-manifests/<code>/conversions
```

2. Update `bridge/app.yml` with your deployed Bridge webhook endpoint URL.

3. Install the App on the `GovernanceOperations` organisation and on any
   other organisations whose GitHub events AIOps should observe.

4. Add the App's private key as a repository secret (`AIOPS_APP_PRIVATE_KEY`).

If you are not connecting GitHub-hosted sources, this step is optional.
AIOps can operate using webhook and GPS Audit Interface sources only.

---

## Step 6 — Enable the workflows

GitHub Actions workflows are enabled by default once the repository has
the required secrets. Verify the suite is running:

1. Go to `Actions` → `AIOps Observability Suite`
2. Click `Run workflow` → `Process: all` → `Run workflow`
3. Watch AO6 complete first (self-monitoring heartbeat)
4. Verify AO1 ingestion runs without errors
5. Check AO7 coverage audit for initial domain coverage status

On first run, expect:
- AO6: heartbeat written to ledger ✓
- AO1: sources polled, gaps logged for any inactive sources
- AO4: Epistemic Field bootstrapped (mostly Unknown region initially)
- AO5: GC measurement produced with conservative defaults
- AO7: coverage gaps identified for any unconfigured domains

The 90-day baseline calibration period begins from first run.

---

## Step 7 — Connect projops

If you are running projops, connect it to AIOps so that ProjOps Governance
Events flow into the governance observability domain:

In your projops `govops.yml`, verify the ledger endpoint is configured:

```yaml
ledger:
  endpoint: "https://your-ledger.example/govops/v1/audit/events"
```

Then in AIOps `aiops.yml`, ensure the `governance` domain includes the
projops ledger as a GPS Audit source:

```yaml
domains:
  governance:
    sources:
      - id: "projops-ledger"
        type: "gps_audit"
        endpoint: "https://your-ledger.example/govops/v1/audit/events"
        auth_secret: "PROJOPS_AUDIT_KEY"
        poll_interval_seconds: 60
        governance_critical: true
```

AIOps will begin polling the projops chain and classifying its Governance
Events into the governance observability domain on the next AO1 run.

---

## Verifying Deployment

A healthy AIOps deployment shows:

- **AO6 heartbeat** written to the Immutable Ledger every 5 minutes
- **AO1** completing without source gap alerts
- **AO4 EFCS** above 0.700 within 90 days of deployment
- **AO7 coverage audit** passing all checks weekly
- **AO5 GC measurements** produced on the normal-mode schedule (weekly)

If AO7 reports gaps, follow the instructions in the `coverage-audit-finding`
issue template to resolve them.

---

## Governance Record

After deployment is stable (all AO7 checks passing for two consecutive weeks),
update `GOVERNANCE.md` to reflect the operational status and record the
deployment Governance Event in your Immutable Ledger as a GPS `system`
operation per GADS §9.
