# Compounding System Design

**Where AI sits in DAII:**

- **Request path (SLM only):** an SLM router for model routing (easy prompts go to a cheap model, hard ones to a strong one), an embedding SLM for semantic caching, and an SLM classifier for security guardrails (PII/PHI/PCI detection, prompt-injection detection, content moderation).
- **Learning path (offline):** an **LLM-as-judge** runs **after** each sampled request has been fully processed, on redacted data, using a self-hosted open-weight model inside the operator's footprint.
  - It **never** influences the current request, so it adds no latency.
  - Its purpose is to learn after the fact and improve how *later* requests are handled.
- **Everything else** (rate limiting, batching, KV-cache-aware and location routing, policy enforcement) is deterministic software with no SLM or LLM.

**What the loops produce:**
- new training and calibration data for the router and guardrail SLMs
- updated routing thresholds and policies
- updated golden-dataset rows and judge rubrics (M4)
- *where the customer opts in*, improved system prompts or retrieval content for the customer's own apps

## Feedback Loops

| Loop | Input | Output | Compounds? | Status |
|------|-------|--------|-----------|--------|
| **Model routing: end-user feedback** | Thumbs up/down; the user repeats a prompt in different words (semantic re-prompt) that then had to be escalated to a more capable model | Misroute labels → router SLM retraining and threshold recalibration | Y | active |
| **Security guardrails: offline judge** | LLM-judge verdict that a guardrail decision was wrong (e.g. PII/PCI/PHI not adequately redacted, a false block) | New labeled samples for the guardrail SLM; new golden-dataset rows | Y | active |
| **Semantic cache: hit validation** | Judge reviews of sampled cache hits; "stale/wrong answer" overrides | Per-category similarity thresholds tightened or relaxed | Y | active |
| **Network-aware location routing** | Operator network telemetry (path latency, congestion) + observed time-to-first-token per location | Location routing defaults updated near real time | Y | active on operator-carried traffic; **missing** off-net |
| **Cross-customer industry learning** | Routing outcomes by task category across regulated customers | Industry routing packs | N (today) | **broken**: blocked by tenant isolation |

**Broken loop identified (self-review; to validate with partner):**

1. **Cross-customer industry learning is broken by design.** Each regulated customer's router starts cold. This is the same weakest loop identified in M2.
2. **A loop that compounds the wrong way at scale.** The routing loop learns mostly from complaints (retries, escalations). If users silently accept mediocre answers instead of retrying, the router reads silence as success and keeps routing *cheaper*. Quality then decays as volume grows.

**Fix plan:**
- For (1): industry routing packs trained on synthetic and de-identified data, plus opt-in federated router updates (weights shared, never prompts). Planned for Horizon 3.
- For (2):
  - Judge sampling that **does not depend on user feedback**, so silence is never counted as success.
  - **About 1% exploration traffic** always sent to the customer's default model as a calibration baseline.
  - An alert on drift velocity (M4).

## Context Connectivity

**How knowledge flows:**
- Customer apps → DAII as **metadata only** (task category, cost, latency, outcome signals).
- The operator's **network operations center** → DAII routing through network telemetry.
- The operator's **security operations center** threat intelligence → guardrail rules (new injection patterns).
- Offline judge verdicts → golden dataset → every router and guardrail release.

**Where it silos:**
1. **Across customers** (by design, for regulation). Prompt content never crosses tenants.
2. **Inside the customer:** individual app teams set their own policies without sharing them. **Fix:** policy templates by team and app in the admin console.
3. **Inside the operator:** the network organization and the AI product organization own separate data platforms and roadmaps. Network telemetry access must be formalized through an internal data agreement and must respect customer proprietary network information (CPNI) rules. This internal silo is the **biggest execution risk** to the network-driven differentiator.

## Governance Policy

**Scope:** All prompts and responses passing through DAII's guardrail layer. This covers PII/PHI/PCI detection and redaction, prompt-injection and jailbreak detection, and content moderation, across all connected models, applications and agents. It also covers routing policies that affect where data goes (residency tags, allowed locations).

**Autonomy boundaries:** Guardrails may automatically redact, mask or block content that matches approved policy rules, without human review. Any of the following requires sign-off from the customer's data privacy and security owners before deployment:
- a new rule category
- a threshold change
- a use-case exception
- a change to residency or location routing

Retrained router or guardrail SLMs need a golden-dataset pass plus ML-ops sign-off (M4).

**Escalation triggers:** These escalate immediately to security and compliance teams:
- repeated or high-confidence prompt-injection or jailbreak attempts from a single user or agent
- any guardrail bypass or failure (fail-open event)
- any detected leakage of regulated data (PHI, PCI, or CPNI on the operator side)
- any routing of residency-restricted data to a non-compliant location

**Audit cadence:**
- **Monthly:** review guardrail logs and redaction actions for false-positive and false-negative rates and rule drift.
- **Quarterly, or after any material incident:** full policy and threshold review, including regulatory alignment.
- **Continuous:** an audit trail of every redaction and routing decision, kept for the customer's required retention period.

**Regulatory exposure (EU AI Act / other):** The lead segment is US healthcare and financial services, so the exposure is **sector-specific**:

| Regime | Why it applies to DAII |
|---|---|
| **HIPAA** | PHI flows through the gateway; a business associate agreement is required. Judge samples must be redacted. |
| **GLBA** and **NYDFS Cybersecurity Regulation** (23 NYCRR 500) | Customer financial data in prompts; access controls and audit evidence. |
| **PCI DSS** | Card data in prompts or agent tool calls puts the gateway in scope unless it is redacted at the first hop. |
| **SEC / FINRA recordkeeping** | Some AI interactions may need to be retained. This conflicts with data minimization, so retention is configured per tenant. |
| **State AI laws** (e.g. Colorado) | Mainly apply to customers' high-risk AI use; DAII supplies evidence (logs, policies). |
| **FCC CPNI rules** | Operator-side: limits on using customers' network data for routing or shadow-AI discovery. Requires customer consent and contract terms. |
| **EU AI Act** | **Low relevance**: US-only launch. A routing and guardrail gateway is not itself a high-risk system. Revisit if the product goes international. |

## Agent Topology

DAII **has no autonomous agents in its own runtime**. Its SLMs are classifiers and an embedding model with bounded outputs (a tier, a location, redact/block/allow). DAII **governs the customer's agents** as clients.

| Actor | Can do | Cannot do | Who approves |
|---|---|---|---|
| Router SLM | Choose model tier and location within policy | Override residency tags, rate limits or guardrail blocks | ML ops (release), customer (policy) |
| Guardrail SLM | Redact, mask, block or flag | Change its own thresholds; whitelist content | Customer privacy/security owners |
| Retraining pipeline | Train candidate models from labeled metadata | Deploy to production | ML ops after golden-dataset pass |
| Offline LLM judge | Score redacted samples after the fact | See unredacted content; affect live requests | Fixed by DAII platform policy |
| Customer agents (clients) | Call models through DAII with an agent identity, per-agent rate limits and guardrails | Bypass guardrails; reach unapproved models or locations | Customer admin |
| *Horizon 3:* agent / MCP tool-call governance | Inspect tool calls and enforce allow-lists | — | Customer admin |

## Shadow AI Audit

| Tool | Owner | Risk Level | Decision |
|------|-------|-----------|----------|
| A customer's own agent already filters PCI (payment card) data before calling models | Customer service workflow (customer side) | M | **Keep.** It's the customer's control. Tell them DAII offers the same capability if they want it, and document the order of operations to avoid double redaction or conflicting outputs. |
| Teams bypassing the gateway and calling AI endpoints directly | Business units, found in sales and customer interviews | **H** | **Govern.** This is a *revenue and value* risk: traffic that bypasses DAII is neither optimized nor protected. On traffic the operator carries, identify connections to unsanctioned AI endpoints from network data, with customer consent and within CPNI rules (planned for Horizon 2). Off-net, partner with DLP and SaaS-discovery tools. |
| DAII's own team using public chatbots with customer data during design | Operator product and engineering team (internal) | H | **Govern.** Only sanctioned enterprise AI tools are allowed. Customer data stays in approved environments. Use is logged. |

**Total tools found:** 3

**Tools after triage:** 3. One is kept as is; two are governed. None are killed.
- The network-based discovery capability is added to the roadmap (Horizon 2).
- Off-net discovery stays with partners.

**Estimated hidden spend:** two different numbers, kept separate:
- **Customer's ungoverned AI spend (illustrative assumption):** if 10–20% of AI traffic bypasses the gateway, about **$10–20K per month** of the reference customer's $100K monthly LLM spend is unoptimized and unprotected.
- **Cost of off-net detection tooling:** about **$5,000 per month per customer** for a partner DLP or SaaS-discovery tool.
