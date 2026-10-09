# The Prototype Bet

## What I Built
<!-- One sentence: what does this prototype demonstrate? -->

The prototype will be a **routing-and-savings simulator**. It replays an anonymized, redacted enterprise prompt log through the DAII router SLM and semantic cache, then shows:

- the mix of model tiers the router chose
- how long each routing decision took
- estimated cost savings compared with the customer's default model
- quality parity, scored by an offline judge

## Tool Used
<!-- v0 / Cursor / Lovable / other -->

## Prototype Link
<!-- Paste the shareable URL -->

## AI Value Archetype
<!-- Automator / Copilot / Oracle / Creator / Orchestrator -->

Similar to Orchestrator.

## The Bet in One Sentence
<!-- What you're building, for whom, why now -->

For  US enterprises already on our network, whose LLM inference bills are growing faster than their budgets, we will build SLM-based inference intelligence that routes each request to the cheapest model and location that still meets quality, latency and data-residency requirements (for enterprises in regulated sector), using network telemetry that no ISV gateway can see.  

The timing is right because routing is proven in research and the market is now pricing this layer in the billions.


## Kill Criteria
<!-- When would you stop? What evidence would kill this bet? -->

We stop, or pivot, if any of the following holds:

1. **Savings too small.** On anonymized logs from at least two design partners, the simulator shows **less than 30% cost savings** while keeping quality parity of 90% or higher. Quality parity means the offline judge rates the routed answer as good as or better than the default model's answer.
2. **Network advantage not real.** On traffic the operator carries, network-aware routing improves p95 end-to-end latency by **less than 10%**, or adds **less than 5%** in cost benefit, compared with network-blind routing. If so, we drop the "network-driven" positioning.
3. **Too slow.** Routing decisions add **more than 50 ms at p95** at a regional point of presence.
4. **Guardrails too weak.** Recall falls **below 90%** on high-severity PII/PHI/PCI and prompt-injection cases, at a false-positive rate of 2% or less.
5. **No demand.** **Fewer than three design partners/customers** sign letters of intent within two quarters.

