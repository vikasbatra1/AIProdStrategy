# Three-Axis Vulnerability Diagnostic

> **Scoring direction used in this repo:** for Contextual Moat and Data Advantage, a higher score is stronger (better for us). For Platform Exposure, a higher score means more exposed (worse for us).

## Product

**Product:** Distributed AI Inference Intelligence (DAII)

DAII is network-driven inference intelligence for large and mid-size US enterprises that already buy network services from a US Tier-1 telecom. It sits in front of every inference request an enterprise generates. For each one, it decides **which model, which location, and which policy** applies. The goal is to lower token cost while maintaining quality, and to add security and privacy.

It works across all of the customer's inference environments: private data centers, hyperscaler (CSP) clouds, neoclouds and edge inference locations.

**Capabilities:**

- **Lower inference cost.** Inference orchestration covers model routing, inference-location routing, KV-cache-aware routing, semantic caching, batching and per-user rate limiting. Cloud egress charges are avoided for traffic kept on-prem or on the operator's network.
- **Network-driven routing (the differentiator).** Wherever the telecom carries the traffic, routing decisions use the operator's own network topology and real-time congestion data. ISV AI gateways (F5, Kong, Portkey, OpenRouter) cannot see this.
- **Data sovereignty for compliance.** The data path stays on the customer's premises or the operator's network, never a third-party SaaS, where required for enterprises in regulated sectors(healthcare, financial) .
- **Lower IP exposure risk.** Prompts are never sent to a hosted intermediary.
- **Lower latency** for latency-sensitive applications.
- **AI security guardrails.** PII/PHI/PCI redaction, prompt-injection detection and content filtering.

DAII is sold standalone or bundled with the operator's existing wireline and wireless enterprise network services.

**How AI is used (SLM, not LLM).** DAII is an AI infrastructure product. Its request path runs three **Small Language Models (SLMs)**:

- an embedding model for semantic caching
- a complexity and intent classifier for model routing
- a classifier for security guardrails

It does **not** use an LLM to process requests. An LLM is used only as an **offline judge**: it reviews a redacted sample of requests after they complete, to improve routing and guardrails for future requests (see M4 and M5).

**Your Role:** Product leader, AI Infrastructure, at a US Tier-1 telecom, accountable for the DAII bet.

---

## Scores

### Contextual Moat: 2/5
*Workflow depth × switching cost. Would users leave in a weekend if a competitor showed up?*

**Score rationale:**

On its own, an AI gateway has weak switching costs. Apps reach it through an OpenAI-compatible endpoint, so replacing it can be as simple as changing a base URL.

The moat comes from what surrounds the gateway:

- network-aware routing on traffic the operator carries
- routing and guardrail policies tuned to each customer, backed by compliance evidence such as audit logs and redaction records
- being part of an existing enterprise network contract that the customer's procurement and security teams have already approved

The score depends on which access the customer buys from the operator:

| Customer's access from the operator | Moat | Why |
|---|---|---|
| Wireline only | 1/5 | Network-aware routing covers only wired sites, and the gateway is easily swapped |
| Wireless only | 2/5 | Some mobile path visibility, but enterprise apps mostly run in data centers and clouds |
| Wireline + wireless | 3/5 | End-to-end path visibility plus a bundled contract |

The lead segment (regulated enterprises in the existing base) is mostly wireline-led, so the **blended score is 2/5**.

**Named attackers:**

- Data center and edge platforms: Equinix, Akamai
- Wireless operators: AT&T, T-Mobile
- Wireline operators, e.g. Comcast
- AI gateway and guardrail ISVs: **F5** (AI Gateway and AI Guardrails, already shipping), Kong, Portkey
- Neutral model routers: **OpenRouter**, which Stripe agreed to acquire on August 19, 2026

---

### Data Advantage: 2/5
*Proprietary signal that compounds with usage. What do you see that OpenAI doesn't?*

**Score rationale:**

By design, DAII does **not** store customer prompt or response content for learning; regulated buyers require that. It does capture proprietary **metadata** that compounds over time:

- **Routing outcomes:** which model tier served which kind of request, at what cost and latency, and whether the user retried or escalated.
- **Network telemetry:** path latency and congestion between enterprise sites and inference locations. Only the operator can see this.
- **Offline judge verdicts** on a roughly 1% redacted sample of requests, used to calibrate routing.

This is up from 1/5 in the first draft. The metadata signal is real, but it is thin compared with the cross-customer data a high-volume neutral router like OpenRouter collects.

**Named attackers:** Equinix, Akamai, AT&T, T-Mobile. Stripe/OpenRouter is the strongest on cross-customer model-performance data.

---

### Platform Exposure: 4/5
*Encroachment risk × pivot speed. If Apple/Google/OpenAI ships your hero feature native — then what?*

**Score rationale:**

**Encroachment risk is high.** Model routing and guardrails are becoming built-in features of other platforms:

- Hyperscalers are adding routing and safety features to their model services.
- F5 already sells an AI Gateway and AI Guardrails, the latter from its CalypsoAI acquisition.
- Stripe is acquiring OpenRouter.

**Pivot speed is slow.** A telecom moves more slowly than an ISV.

Two things cap the exposure. Native hyperscaler routing only covers that provider's own models and regions. And none of these competitors can use the operator's network path data or keep the data path on the operator's network.

Exposure also differs by access type:

- **Wireline:** if hyperscalers build data centers close to enterprises, exposure stays moderate, because the WAN path between them is still the operator's.
- **Wireless:** exposure is high, because the edge footprints of hyperscalers and Akamai compete directly for mobile and edge inference placement.

**Named attackers:** AWS, Google, Microsoft, Oracle (native routing and guardrails); Stripe/OpenRouter; F5.

---

## Top Vulnerability

Routing and guardrails are becoming commodity features of hyperscaler platforms and neutral routers (F5, Stripe/OpenRouter). DAII's defensibility therefore rests entirely on **network-driven intelligence, on-net sovereignty and regulated-industry trust**, not on the gateway itself.

## Confidence Level

**M (Medium).** Three things support the bet:

- The customer pain is real.
- Research shows large savings from routing: RouteLLM reports 35–85% depending on the workload.
- The market is validating the layer, through Stripe/OpenRouter and F5/CalypsoAI.

What is still unproven is whether *network-aware* routing adds measurable value beyond network-blind routing. That is an explicit kill criterion in `prototype.md`.

### Sources
- Stripe agrees to acquire OpenRouter (Aug 19, 2026): https://siliconangle.com/2026/08/19/stripe-buys-ai-model-router-openrouter-in-reported-7-5b-deal/
- F5 completes CalypsoAI acquisition, introduces F5 AI Guardrails: https://www.f5.com/company/blog/what-are-ai-guardrails
- RouteLLM (LMSYS / UC Berkeley): https://lmsys.org/blog/2024-07-01-routellm
