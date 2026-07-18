# Bridge — Analysis Engine

Classifies incoming observability records, assigns Epistemic Field regions,
scores anomalies, and routes to consumers.

## Processing Pipeline

Input → Schema Validation → Provenance Verification → EF Assignment →
Anomaly Pre-scoring → Synthetic Detection → Correlation → Router

**EF regions:** confirmed (verified, no disagreement) | disputed (failed
or disagreement) | unknown (no data for domain item)

**Anomaly scoring:** Z-score vs 90-day rolling baseline per domain.
Tiers: informational (1-2σ) | notable (2-3σ) | significant (3-5σ) | critical (>5σ)

**Synthetic detection:** 4 methods per record — statistical distribution,
temporal sequence, cross-source consistency, provenance chain.

**Correlation:** 6 mandatory patterns per GADS §4.3, 5-minute check interval,
60-minute sliding window.

## Implementation (v0.1)

Requires 90 days operational data for accurate baselines. Conservative
defaults from `configs/anomaly-baselines.yml` apply until then.

**Targets:** classification latency < 30s | baseline recomputed daily |
correlation checked every 5 minutes.

**Technology:** Python (scikit-learn, scipy) or Go. Stateless service —
state in Observability Store, not in-process.
