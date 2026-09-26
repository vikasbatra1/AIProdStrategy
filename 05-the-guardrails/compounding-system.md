# Compounding System Design

Assume the AI Gateway uses SLM for Model Routing (sending easy prompts to a cheap model and hard ones to a strong one)
and SLM for Security Guardrails (PII detection, prompt-injection detection, and content moderation) with LLM as a judge. The LLM judging happens post response and not during the processing to remove any latency impact. The motivation for LLM as judge is that learning can happen after the processing is done.

Rest of the capabilities are implemented without using and SLM or LLM. 

The possible outputs are additional data for RAG, next round of Model fine tuning, improved System Prompt, updated golden parameter set for LLM as judge evalautaion. 


## Feedback Loops

| Loop | Input | Output | Compounds? | Status |
|------|-------|--------|-----------|--------|
| Model Routing - Customer Feedback| Customer Thumbs up or down, End user repeat prompt that stopped after model escalation| Update the SLM data   | Y/N | active |
| Security Guardrails |LLM as a judge output stating quality was low e.g. PII was not filtered out   | Update Sample data for SLM | Y | active /  |
| | | | Y/N | active / broken / missing |

**Broken loop identified by partner:**
**Fix plan:**

## Context Connectivity
<!-- How does knowledge flow across teams and domains? Where does it silo? -->

## Governance Policy

**Scope:** All prompts and responses passing through the AI Gateway's guardrail layer, covering PII/PHI/PCI detection and redaction, prompt injection and jailbreak detection, and content moderation, across all connected models, applications, and agents. 
**Autonomy boundaries:** Guardrails may automatically redact, mask, or block content matching approved policy rules without human review; any new rule category, threshold change, or use-case exception requires sign-off from data privacy and security stakeholders before deployment. 
**Escalation triggers:** Repeated or high-confidence prompt injection/jailbreak attempts from a single user or agent, any guardrail bypass or failure (fail-open event), and any detected leakage of regulated data (e.g., CPNI) escalate immediately to security and compliance teams. 
**Audit cadence:**
Guardrail logs and redaction actions are reviewed monthly for false positive/negative rates and rule drift; a full policy and threshold review, including regulatory alignment, occurs quarterly or after any material incident.
**Regulatory exposure (EU AI Act / other):**
Sector specific for Healthcare and Financial sectors.

## Agent Topology
<!-- If using agents: what can each agent do? What can't it do? Who approves what? -->

## Shadow AI Audit

| Tool | Owner | Risk Level | Decision |
|------|-------|-----------|----------|
| The PCI Data (Payment Card Data) is being filtered out by a customer's own agent  | Customer Service - Workflow |  M | Ignore - communicate to customer the capability exists in the AI Gateway, if they choose to use it |
| Teams bypassing the Gateway | Sales and Customer interviews - Capability | H | Partner  - A separate tool for Detection , via network/DLP tools identifying traffic to unsanctioned AI endpoints, and via IT asset/SaaS discovery for unauthorized AI subscriptions|
| | | H / M / L | keep / govern / kill |

**Total tools found:**
2 
**Tools after triage:**
0, No capabilities to be built into the product.
**Estimated hidden spend:**
  $5000/M per customer to install the Detection tool.
