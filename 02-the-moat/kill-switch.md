# Kill Switch Audit

> **SLM note:** This template assumes a product built on a third-party LLM API, where the main lock-in risk is that one API provider.
> **Not Applicable for DAII:** DAII is an AI infrastructure product whose runtime uses self-hosted, open-weight Small Language Models (SLMs). It does not use an LLM API in the request path.
> DAII's real dependencies are listed instead: open-weight SLM base models, the GPU and serving stack, the self-hosted offline judge, and the upstream LLM providers the *customer* chooses.

## Vendor Dependency Assessment

| Dimension | Current State | Risk Level | 48-Hour Action |
|-----------|--------------|------------|---------------|
| **Provider** | Runtime SLMs (router classifier, embedding model, guardrail classifier) use **open-weight models, self-hosted** at operator points of presence. The offline judge is a self-hosted open-weight mid-tier LLM. GPUs are primarily NVIDIA. Upstream LLMs are chosen and paid for by the customer, not DAII. | **M** (NVIDIA concentration); **L** for models | Keep ONNX exports and a CPU inference path for the classifier SLMs. Keep a second qualified open-weight model for each SLM role and for the judge. |
| **Abstraction** | Customer apps call one **OpenAI-compatible endpoint**. Adapters normalize the APIs of each upstream provider (hyperscaler model services, frontier labs, neoclouds, on-prem). | **L** | Run the adapter conformance tests for the top 10 providers. Add a new provider adapter within 48 hours of a customer request. |
| **Routing** | The router SLM is trained on our own routing outcomes plus public preference data. Thresholds are configurable per customer. The model catalog changes constantly as new models launch, so the router needs recalibrating. | **M** | Automated recalibration against the golden dataset (M4) whenever a model is added or repriced. If the router fails, fall back to static rule-based routing. |
| **Eval** | We own the golden dataset (M4). The judge is self-hosted and version-pinned. Risks are judge drift or a change to the judge model's license. | **M** | Pin the judge version. Keep two validated judge candidates and re-score the golden dataset against both before any switch. |

## Portability Score
**Partial.**
- The **model layer is Ready:** every SLM and the judge are open-weight and self-hosted, and customers' upstream LLMs can be swapped behind one endpoint.
- The **infrastructure layer is Partial:** the serving stack and point-of-presence GPU footprint are tuned for NVIDIA. Moving the classifiers to CPUs or other accelerators is feasible but would cost latency headroom.

DAII is also a **portability layer for the customer**: their apps never depend on a single LLM provider. This is part of the value proposition.

## If [primary vendor] doubles pricing tomorrow

DAII has two "primary vendors," so both cases are covered.

**A. NVIDIA, or the GPU cloud supplying PoP capacity, doubles prices.**
- Runtime SLM inference is a small share of cost of goods sold (COGS): about $1 per million requests (see M3). The offline judge is the largest GPU consumer.
- **48-hour response:**
  1. Cut the judge sampling rate from ~1% to ~0.5%.
  2. Move the classifier and embedding SLMs to CPU or quantized inference where p95 latency stays under 50 ms.
  3. Delay non-urgent router retraining.
- **Margin impact:** a drop of about 2–3 points. Pricing is unchanged.

**B. The customer's dominant upstream LLM provider doubles prices.**
- **DAII's costs are unaffected:** we don't resell tokens, so there is no token pass-through to absorb.
- Our platform-fee revenue is unchanged, and the customer's savings from routing **increase**, which strengthens renewal.
- **48-hour response:** publish an updated routing policy that shifts traffic toward alternative providers and on-prem or neocloud models, and show the avoided cost in the savings report.

## If [primary vendor] ships a competing product

**Scenario:** a hyperscaler or frontier lab ships native "auto-routing" across its own models, or F5 or Stripe/OpenRouter add network-aware features.

**What stays defensible:**
1. **Cross-provider, cross-location routing.** A provider's native router only covers its own models and regions. DAII routes across all of the customer's environments: on-prem, multiple clouds, neocloud and edge.
2. **Network-aware routing using operator telemetry.** Path latency and congestion on the operator's network are not visible to an ISV or a hyperscaler.
3. **On-net sovereignty.** The data path never leaves the operator's network or the customer's premises, which is critical for HIPAA, GLBA, PCI DSS and NYDFS evidence.
4. **Commercial bundling** with existing enterprise network contracts: procurement already approved, one bill.
5. **Co-opetition.** A competitor's native router can become one of DAII's routing targets rather than a replacement for DAII.
