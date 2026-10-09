# Data Flywheel Map

> Score each loop 1-5. Your weakest loop is where competitors attack first.

**Scope:** This map covers DAII's **cost-optimization** capabilities, the core software value: model routing, semantic caching, KV-cache-aware routing, inference-location routing, batching and rate limiting. The guardrail feedback loop is covered in `05-the-guardrails/compounding-system.md`. All capabilities are planned (DAII is a future product), so "captured" means "captured at launch."

**Privacy constraint for all loops:** regulated customers require that prompt and response content is never kept for training. Every loop below learns from **metadata, user actions and offline judge verdicts on redacted samples**, never from raw content.

## Flywheel Loops

| Loop | What It Measures | Score 1 | Score 5 | Score |
|------|------------------|---------|---------|-------|
| **Correction** | Do users fix AI outputs? Is that signal captured and reused? | No capture | Automated retraining | 3/5 |
| **Preference** | Does the product learn individual / team preferences over time? | Stateless | Deep personalization | 2/5 |
| **Domain Context** | Does usage in one area improve quality in adjacent areas? | Siloed | Cross-domain transfer | 3/5 |
| **Network** | Does each new user / team make the product better for everyone? | Isolated | Strong network effects | 3/5 |

### Correction Loop: 3/5
**What we capture:**
- **Explicit signals:** thumbs up/down where the customer's app exposes them, and an optional "retry with a stronger model" button.
- **Implicit signals:** the user re-asks the same question in different words (detected by the embedding SLM), or the request escalates to a higher tier after a low-quality answer. Each of these is labeled a probable **misroute**.
- **Latency complaints:** if the response arrived too late, the router's cost-versus-speed balance for that request class shifts toward faster, more expensive options.
- **Offline LLM-judge verdicts** on a ~1% redacted sample, run after the request has completed.

**How it compounds:** misroute labels and judge verdicts feed periodic retraining of the router SLM and recalibration of its thresholds. Each retraining cycle lowers the misroute rate, so more traffic can safely go to cheaper tiers. **Not 5/5** because retrained routers are deployed only after a human approves them (see the Human-in-the-Loop section in M4); this is deliberate for regulated customers.

### Preference Loop: 2/5
**What we capture:** a preference per user, team or application: **minimize cost**, **maximize accuracy** or **minimize latency**, set explicitly or inferred from repeated overrides. It is saved and applied by the routing logic for that user or session.

**How it compounds:** preferences tune the routing threshold for each user, team or app, so fewer users override the router over time. Mostly this is settings that persist rather than learned personalization, hence 2/5.

### Domain Context Loop: 3/5
**What we capture:** within one customer, routing outcomes by task category (summarization, extraction, code, regulated-document Q&A).

**How it compounds:** inside one customer, what the router learns in one application carries over to the customer's other applications with similar task categories. 
Across customers, 
- for regulated customers, it **does not carry over**: tenant isolation in regulated industries keeps learning inside each customer. This is the right policy, but it caps the loop. 
- For non-regulated customers, it **does carry over**: increasing the compounding.    

### Network Loop: 3/5
**What we capture:**
- **Shared model-performance metadata across customers:** latency, error and outage rates, and judge-scored quality per model and task category. No content.
- **Operator network telemetry:** path latency and congestion between enterprise sites and inference locations. This is unique to us.

**How it compounds:** each new customer improves the routing defaults for every customer (which model is degraded right now, which location is congested), and each new site on the network adds path data. **Honest comparison:** a large neutral router such as OpenRouter has far more cross-customer model data. Our edge is the network telemetry, not the model data.

**Total Flywheel Score: 11/20**

**Weakest Loop:** Domain Context for regulated customers. Learning cannot cross customers, because regulated tenants are isolated.

**Fix for weakest loop:**

---

## Encroachment Threat Assessment

### 1. Platform Encroachment
**Attacker:** Akamai (inference across its edge footprint) and Equinix (Distributed AI Hub: private interconnection to third-party GPU clouds). They are not model providers, so, like us, they can credibly offer *neutral* routing. A model provider's own router naturally favors its own models.

**Vector:** add routing and guardrails at the edge and colocation sites where enterprise AI workloads already connect to clouds. Equinix already has customer presence in its data centers and interconnection points. A further risk: an ISV like F5 runs its gateway as a service on top of these footprints.

**Time-to-threat:** already underway. Both are building out larger infrastructure.

**% of value at risk:** ~60%. Close to 100% of the *standalone gateway* value is at risk for traffic the operator doesn't carry. The network-aware routing and on-net sovereignty value is protected wherever the operator carries the traffic.

### 2. Vertical Competitor
**Attacker:** **Stripe, with OpenRouter.** Stripe agreed to acquire OpenRouter on August 19, 2026, in a deal reported at $7.5B or more. OpenRouter routes across 400+ models from 80+ providers, and the two companies already shipped a token-billing integration.

**Vector:** a neutral multi-model router bundled with token metering and billing. Developer-first, with a one-line integration.

**Time-to-threat:** about 90 days.

**% of value at risk:** ~50% of routing value in the broad market, but **~25% in our regulated lead segment**. OpenRouter is a hosted third party that sits in the data path, which works against HIPAA, GLBA and PCI requirements on where data goes.

### 3. Adjacent Expansion
**Attacker:** **F5 is no longer adjacent; it is a direct competitor today.** F5 completed its CalypsoAI acquisition in September 2025, launched F5 AI Guardrails and AI Red Team in January 2026, and also sells the F5 AI Gateway. It already sells to existing API gateway and application-delivery customers.

The *adjacent* threat is now **Cloudflare**, an internet-edge network that already runs an AI Gateway and could push into regulated enterprises.

**Vector:** F5 cross-sells to its large installed base of BIG-IP and NGINX customers. Cloudflare bundles AI Gateway into its zero-trust and SASE contracts.

**Time-to-threat:** F5 now; Cloudflare 6 months.

**% of value at risk:** ~50%. Both can match routing and guardrails as software. Neither sees the operator's private network path, and neither can bundle with WAN or wireless access.

---

## 90-Day Encroachment Plan

*This is the attacker's plan as played in the partner exercise.*

**Attacker:** Stripe with OpenRouter.

**Attack vector (target the weakest loop):** Domain Context. OpenRouter's cross-customer volume gives it routing data for every industry from day one, while DAII starts each regulated customer from a cold, isolated router.

**Weeks 1-4: what they ship.** An enterprise tier with zero data retention, bring-your-own-keys, a HIPAA business associate agreement and SOC 2 reporting. Also token billing folded into Stripe's existing finance workflows.

**Weeks 5-8: how they poach users.** A neutral pitch: "We have no data centers to prefer, so we route purely on cost and quality." Discounted token prices based on aggregated volume, and a one-line base-URL change.

**Weeks 9-12: why users don't come back.** Metering and billing become embedded in the customer's finance processes, and routing improves with cross-customer data. Switching back would mean re-integrating billing and losing the tuned routing.

**Your defense:**
1. **The data path never leaves the operator's network or the customer's premises.** This produces sovereignty evidence that holds up in HIPAA, GLBA and NYDFS audits, which a hosted router cannot offer.
2. **Network-aware routing**, using path and congestion data that a neutral router cannot see.
3. **Bundling into the existing enterprise network contract:** procurement is already approved and the customer gets one bill.
4. **Co-opetition:** DAII can use OpenRouter as one upstream provider among many, which reduces the reason to switch.
5. **Close the Domain Context gap** with vertical routing packs.

### Sources
- Stripe agrees to acquire OpenRouter: https://siliconangle.com/2026/08/19/stripe-buys-ai-model-router-openrouter-in-reported-7-5b-deal/ · https://aiunderstanding.org/news/stripe-agrees-to-acquire-openrouter-to-expand-ai-model-routing
- F5 AI Guardrails / CalypsoAI: https://www.f5.com/company/blog/what-are-ai-guardrails · F5 product timeline: https://data.larridin.com/data/ai-tracker/company/f5/
