# Three-Horizon Roadmap & Board Pitch

## Roadmap

### Horizon 1 — Now (0-3 months)
*Quick wins. Ship with existing capabilities.*

| Initiative | Metric | Confidence |
|-----------|--------|-----------|
| Design-partner program with 3 regulated customers (healthcare, financial services) from the existing enterprise base | 3 signed letters of intent (kill criterion 5) | M |
| Routing-and-savings simulator (the prototype) run on partners' anonymized logs, with RouteLLM as the baseline router | ≥35% savings at ≥90% quality parity; kill if under 30% | M |
| Gateway MVP at 2 regional points of presence: model routing, semantic cache, rate limiting, PII/PHI/PCI redaction, all on open-weight SLMs | p95 decision overhead ≤50 ms; guardrail recall ≥95% on the golden dataset | H |

### Horizon 2 — Next (3-9 months)
*Bets. Requires new capabilities or integrations.*

| Initiative | Metric | Confidence |
|-----------|--------|-----------|
| **Network-aware location routing** using the operator's network telemetry (needs the internal network-data agreement) | ≥10–15% p95 end-to-end latency improvement vs. network-blind routing on operator-carried traffic (kill criterion 2) | M |
| General availability of tiered platform pricing, plus the Observability add-on with a savings report that is *reported, not billed* | 10 paying customers; blended gross margin ≥70% | M |
| Bundled offering with enterprise network contracts | Attached to 20% of the regulated-segment network pipeline | M |
| Advanced Security Guardrails add-on, plus network-based shadow-AI discovery (with customer consent, within CPNI limits) | Add-on attach ≥30%; share of customer traffic governed ≥80% | L |

### Horizon 3 — Bet (9-18 months)
*Moonshots. High uncertainty, high potential.*

| Initiative | Metric | Confidence |
|-----------|--------|-----------|
| Industry routing packs + opt-in federated router learning, to fix the weakest flywheel loop | Domain Context loop 2/5 → 4/5; new-customer time-to-target-savings under 2 weeks | L |
| Agent and MCP tool-call governance (agent identity, tool allow-lists, inspection of tool-call chains) | ≥5 customers governing agent traffic through DAII | L |
| Edge and distributed GPU placement: route latency-sensitive inference to the operator's own edge inference locations | Edge-routed share of traffic; p95 time-to-first-token on edge vs. regional | L |

## Board Pitch

**Thesis (1 sentence):**
Distributed AI Inference Intelligence turns a US Tier-1 telecom's network into the control point for enterprise AI. Its SLM-based routing and guardrails cut regulated enterprises' inference costs by 35–60% at equal quality, keep their data on-net, and use network intelligence no AI gateway vendor can replicate.

DAII orchestrates inference across the customer's on-premises data centers, hyperscaler (CSP) environments, neoclouds and edge locations. Its capabilities:
- **Lower inference cost:** inference intelligence decides which location or server cluster serves each request, using model routing, KV-cache-aware routing, prompt and semantic caching, batching and rate limiting. Routing also accounts for network topology and congestion wherever the operator provides the network.
- **AI Security Guardrails** (add-on): PII/PCI/PHI redaction, prompt-injection and jailbreak detection, content moderation.
- **Observability** (add-on): cost per user and per group within the enterprise, security events, compliance reporting.

**The case:**
1. **Why now.** Customers' inference bills are scaling faster than their budgets. Routing is proven: RouteLLM cut costs 35–85% depending on workload while keeping 95% of GPT-4 quality. The market is pricing this layer at strategic value: Stripe agreed to acquire OpenRouter for a reported $7.5B+, and F5 bought CalypsoAI to build F5 AI Guardrails.
2. **What's defensible.** Not the gateway, which is commoditizing. What is defensible:
   - network-aware routing on operator-carried traffic
   - on-net sovereignty that produces HIPAA, GLBA and PCI audit evidence a hosted router cannot
   - existing relationships and approved contracts with regulated enterprises
   - bundling with network services
3. **The economics.**
   - A tiered platform fee by governed requests, with **no share-of-savings component**. Savings are reported, not billed, so there are no baseline disputes and procurement gets forecastable spend.
   - Security and Observability are add-ons priced separately.
   - Illustrative: about $500K ARR per customer at about 83% blended gross margin; break-even at about 15–19 customers; about $20M ARR at 40 customers.

**The risks:**
1. **Trust / failure modes.** Savings may come in lower than promised, or misroutes may hurt quality. *Mitigation:* the kill criteria are tested on the simulator before scaling; the router fails up to a stronger model when unsure; a quality-parity contract with alerts (M4).
2. **Scale / governance.**
   - Cross-user semantic cache leakage
   - Regulated data appearing in judge samples
   - A routing loop that drifts cheaper when users stay silent
   - The internal silo between the network and AI organizations

   *Mitigation:* permission-scoped cache, redacted self-hosted judge, judging that doesn't rely on user feedback plus exploration traffic, and a formal network-data agreement (M5).
3. **Competitive.** F5 (shipping AI Gateway and Guardrails today), Stripe/OpenRouter (neutral router, about 90 days to threat), hyperscaler native routing, and Akamai/Equinix/Cloudflare at the edge. *Mitigation:* compete where they can't (network data, on-net sovereignty, bundling), and treat their routers as upstream targets, not replacements.
4. **Token-price deflation.** As tokens get cheaper, the savings story weakens. *Mitigation:* the value mix shifts to sovereignty, security and observability; tiers are re-banded on volume.

**The ask (illustrative):**
- **About $6M and about 20 FTEs** for 18 months (Horizons 1–2)
- GPU capacity at **2 regional points of presence**
- **Executive sponsorship** for the network-telemetry data agreement between the network and AI product organizations
- Enterprise sales coverage for **3 regulated design partners**

Funding is released in stages:
- **Gate 1 (month 3):** simulator kill criteria met and 3 letters of intent signed
- **Gate 2 (month 9):** network-aware routing benefit proven and 10 paying customers

## M1 Baseline vs. Now
*Your 3-sentence AI strategy from Module 1 vs. what you'd say now:*

**M1 baseline:** Invest in a high-growth area with potential to generate higher margins with scale. Offer distributed AI inference across private data centers, clouds, neoclouds and edge to lower token cost, with sovereignty, lower latency and security. Combine it with the operator's wireline and wireless services.

**Now:** The gateway itself is commoditizing, with F5, Stripe/OpenRouter and hyperscalers all shipping routing and guardrails. So we win only where they can't follow: network-driven routing, on-net sovereignty, and trusted relationships with regulated enterprises already on our network. We price it as a predictable tiered platform fee, with savings proven in reports rather than billed. We gate investment on hard kill criteria that test whether the network really adds value.

### Sources
- RouteLLM: https://lmsys.org/blog/2024-07-01-routellm
- Stripe / OpenRouter: https://siliconangle.com/2026/08/19/stripe-buys-ai-model-router-openrouter-in-reported-7-5b-deal/
- F5 AI Guardrails / CalypsoAI: https://www.f5.com/company/blog/what-are-ai-guardrails
