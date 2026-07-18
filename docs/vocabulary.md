# AIOps Vocabulary

Plain-language definitions for terms used in the AIOps repository.
For the full normative definitions, see GADS v0.1 and GOS v0.9.

---

**AIOps**
The sixteenth GOS discipline. It observes what all fifteen other disciplines
do, detects anomalies, and feeds GC measurements to the Centre. Think of it
as the governance system watching itself.

**Analysis Engine**
The component inside the AIOps Bridge that classifies incoming data, scores
anomalies, and routes records to the right consumers. In v0.1 the architecture
is specified but implementation is partially stubbed — the `# In production:`
comments show where production code goes.

**Anomaly Baseline**
The statistical model of what "normal" looks like for each observability domain.
Built from 90 days of operational data. Before 90 days, AIOps uses conservative
defaults from `configs/anomaly-baselines.yml`.

**Anomaly Tier**
The severity classification for a detected anomaly: informational (1–2σ),
notable (2–3σ), significant (3–5σ), critical (>5σ). Determines the GPS
alert severity produced.

**CA (Capacity to Act)**
One of the four GC components. Measures the governance system's ability to
translate decisions into operational reality. Fed by CD DORA metrics,
PlatformOps provisioning compliance, and NetworkGov ZTA progress.

**Certification Hash**
A cryptographic hash on an item in the Confirmed EF region, linking it to
the Multi-Observer Verification event that confirmed it. Expires after the
staleness window (365 days for Class 1 data, 180 days for Class 2/3).

**Collector**
A domain-specific configuration template that tells AIOps how to receive
data from a particular observability source. See `collectors/`.

**Confidence Factor**
A multiplier on Raw GC that accounts for measurement quality: CF = OAS ×
MU_factor × EFCS. Effective GC = Raw GC × CF. If your baselines are good
and your Epistemic Field is well-covered, CF approaches 1.0.

**Confirmed region**
The Epistemic Field region containing facts that have passed Multi-Observer
Verification with no Disagreement Condition.

**Cross-domain correlation**
Detecting patterns that span multiple observability domains and are invisible
when domains are watched separately. Defined in `configs/correlation-rules.yml`.
Six mandatory patterns, plus optional organisation-specific rules.

**Disagreement Condition**
Triggered when independent observers reach different conclusions about the
same governance fact. Moves the affected fact from Confirmed to Disputed in
the Epistemic Field.

**Disputed region**
The Epistemic Field region containing facts where independent observers
disagree. Disputed facts cannot support Irreversible governance decisions
at full Observer Sufficiency.

**EFCS (Epistemic Field Coverage Score)**
EFCS = Confirmed / (Confirmed + Disputed + Unknown). Measures how much of
the EF is in the Confirmed region. A key input to the Confidence Factor.

**Effective GC**
Raw GC × Confidence Factor. This is what actually goes into GPS governance
context fields. Lower than Raw GC when measurement quality is uncertain.

**Epistemic Field (EF)**
The governance system's continuously updated map of what it knows (Confirmed),
what is contested (Disputed), and what it does not know (Unknown). AO4
maintains it. Think of it as the governance equivalent of a confidence score
on every fact the system relies on.

**F (Fidelity)**
One of the four GC components. Measures whether the Governing Entity does what
it says. Fed by LegalOps compliance, PolicyOps coverage, SocietalOps feedback,
CD pipeline compliance, FinGov authority compliance, InfraConfig declared state.

**GC (Governance Capacity)**
GC = F × CA × II × AT. The four-component multiplicative measure of governance
strength. Non-compensatory — no component can compensate for a weak one.

**GPS (GovOps Protocol Specification)**
The protocol that AIOps uses to write Governance Events to the Immutable
Ledger. All AIOps outputs are GPS messages (sense, alert, record, evaluate,
escalation, adapt).

**II (Institutional Integrity)**
One of the four GC components. Measures the integrity of the governance
institution itself. Fed by SecOps posture, ITSM infrastructure availability,
FinGov authority structure, AI Governance model coverage, network segmentation.

**Immutable Ledger**
The append-only record of all Governance Events. AIOps writes to it;
the Oversight Body reads from it independently. Nothing is ever deleted
or modified.

**MU_factor (Measurement Uncertainty Factor)**
The confidence component based on instrument class composition. Class 1
instruments (primary independent) produce higher MU_factor than Class 2
(corroborating) or Class 3 (proxy). Part of the Confidence Factor.

**OAS (Observer Agreement Score)**
Measures how much independent observers agree on GC component values.
Higher agreement → higher OAS → higher Confidence Factor → higher Effective GC.

**Observability Store**
AIOps' time-bounded working data store for all ingested observability records.
Separate from the Immutable Ledger. Data here is retained per
`configs/retention-policy.yml`. Governance Events are written to both.

**Observer gap**
When a registered source stops producing data. AIOps treats observation gaps
as governance signals — something is either broken or deliberately hidden.
Produces GPS `alert` of type `observer_gap`.

**AT (Adaptive Transparency)**
One of the four GC components. Measures the governance system's capacity to
detect changes and remain observable. Fed by DataOps lineage, ITSM patch
compliance, SocietalOps non-retaliation status, AIOps own observability
coverage (AO-AT-I1, AO-AT-I2).

**Source registry**
`.github/govops/source-registry.json`. AIOps refuses data from any source
not listed here with `status: active`. Registration is a governance act —
it decides what the governance system trusts as an observation source.

**Synthetic observation**
A fabricated or manipulated observation submitted to mislead the governance
system. AO3 runs four detection methods: statistical distribution, temporal
sequence, cross-source consistency, provenance chain. Detected synthetic
observations are quarantined in the Disputed EF region.

**Unknown region**
The Epistemic Field region containing governance domains where AIOps has
no observations. Unknown regions are not passive — they reduce EFCS and
are flagged by AO4 and AO7. A governance-critical domain Unknown for
more than 7 days triggers Oversight Body escalation.
