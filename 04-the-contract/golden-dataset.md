# Golden Dataset & Reliability Contract

Offer a

## Golden Dataset Spec

| # | Input | Expected Output | Edge Case? | Judge Type |
|---|-------|----------------|-----------|-----------|
| 1 | | | Y/N | rule / LLM |
| 2 | | | Y/N | rule / LLM |
| 3 | | | Y/N | rule / LLM |
| 4 | | | Y/N | rule / LLM |
| 5 | | | Y/N | rule / LLM |

**Adversarial rows included:** __
**Coverage gaps identified by partner:**

## Confidence UX Design

**Approach:** show uncertainty / tiered confidence / human-in-loop trigger

**High confidence (>90%):**
**Medium confidence (70-90%):**
**Low confidence (<70%):**

**User control surface:**

## Reliability Contract

Actual Cost Savings for a day for a given user session.

Benchmark Cost Savings for a day  for a given user session:  Use a separate SLM to make the routing decision (after actual routing has been been done) and recompute cost savings and Compare with the same benchmark as used for Pricing. Computed on a daily basis.


| Metric | Target | Measurement | Alert Threshold |
|--------|--------|-------------|-----------------|
| Accuracy | 90% + Cost Savings vs. LLM as a judge| Use a separate SLM to make the routing decision (after actual routing has been been done) and recompute cost savings and Compare with the same benchmark as used for Pricing. Computed on a daily basis. Where accuracy is same or better inference response accuracy.   | at 98%, 80%, 50% |
| Hallucination rate | | | |
| Latency (p95) | 50 msec | Time to make the routing decision for every inference request ( not token)  | 100 msec, 80 msec and 60 msec|
| Drift velocity | <0.5%/week  | 4 week rolling trend | |

## HITL Architecture
<!-- When does a human step in? What's the escalation path? -->

## Red-Team Findings
*What failure mode did your partner find that you missed?*
