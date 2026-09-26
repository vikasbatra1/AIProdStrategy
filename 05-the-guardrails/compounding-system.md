# Compounding System Design

Assume the AI Gateway uses SLM for Model Routing (sending easy prompts to a cheap model and hard ones to a strong one)
and SLM for Security Guardrails (PII detection, prompt-injection detection, and content moderation) with LLM as a judge. The LLM judging happens post response and not during the processing to reduce latency impact. The motivation is learning  
that can happen after the processing is done.

Rest of the capabilities are implemented without using and SLM or LLM. 

The possible outputs are additional data for RAG or next round of Model fine tuning, improved System Prompt, updated  golden parameter set for Eval. Model config settings.


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

**Scope:**
**Autonomy boundaries:**
**Escalation triggers:**
**Audit cadence:**
**Regulatory exposure (EU AI Act / other):**

## Agent Topology
<!-- If using agents: what can each agent do? What can't it do? Who approves what? -->

## Shadow AI Audit

| Tool | Owner | Risk Level | Decision |
|------|-------|-----------|----------|
| | | H / M / L | keep / govern / kill |
| | | H / M / L | keep / govern / kill |
| | | H / M / L | keep / govern / kill |

**Total tools found:**
**Tools after triage:**
**Estimated hidden spend:**
