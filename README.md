# Joule Enterprise Agent — Supplier Management
### Improving LLM relevance in enterprise contexts using Joule Skills and Agents in S/4HANA

---

## The Problem

Supplier management in SAP S/4HANA traditionally requires navigating 
multiple applications, mastering complex UI flows, and cross-referencing 
data across several screens. There is no conversational layer — every 
action requires deliberate navigation, regardless of how simple the 
underlying intent is.

For procurement and risk teams dealing with dozens of supplier decisions 
daily, this friction is real and costly.

---

## The Approach

Using SAP Joule Studio, I designed a set of Skills and a decisional Agent 
that allow enterprise users to manage suppliers entirely through natural 
language — directly within the Joule interface they already use.

A user can now simply say:
> *"Can you check supplier 12300010 and tell me if we should block them?"*

And the agent will investigate, evaluate, reason, and ask for confirmation 
before taking any action.

---

## Architecture
User (natural language)
↓
Joule Agent
(reasons, investigates, recommends)
↓
┌─────────────────────────┐
│ Joule Skills │
│ ┌───────────────────┐ │
│ │ Evaluation Skill │ │ ← Fetches scoring data from S/4HANA
│ └───────────────────┘ │
│ ┌───────────────────┐ │
│ │ Block/Unblock │ │ ← Executes action after confirmation
│ │ Skill │ │
│ └───────────────────┘ │
└─────────────────────────┘
↓
SAP Build Process Automation Actions
↓
S/4HANA APIs

---

## What It Does

### Sprint 1 — Block / Unblock Skill
A Joule Skill that wraps the SAP Build actions for supplier 
blocking and unblocking. The user provides only the supplier 
number. Joule retrieves the current status, presents a summary, 
and explicitly asks for confirmation before executing any action.

**No blocking action runs without explicit user confirmation.**

### Sprint 2 — Evaluation Skill  
A Skill that retrieves supplier scoring data from S/4HANA and 
surfaces it in a readable format — score values, reliability 
indicators, compliance status — interpreted in plain language 
for a business user.

### Sprint 3 — Decisional Agent
An Agent that orchestrates both skills autonomously: it 
investigates the supplier (evaluation), optionally enriches 
with external data (web, third-party sources), produces an 
argued recommendation, and only then offers to execute the 
block/unblock via the appropriate Skill.

---

## Key Design Decisions

**Human-in-the-loop by design**  
No sensitive action (blocking a supplier) executes without an 
explicit confirmation step in the conversation. This was a 
non-negotiable constraint from the start.

**Skill description quality is critical**  
The agent's ability to select the right Skill at the right moment 
depends entirely on how precisely the Skill's intent and parameters 
are described. Vague descriptions lead to wrong tool selection — 
this is one of the most underestimated levers for LLM relevance 
in enterprise contexts.

**Evaluation and action Skills are intentionally decoupled**  
Keeping them separate allows the agent to investigate without 
committing, and lets users request an evaluation independently 
of any action intent.

**Internal data first, external enrichment optional**  
S/4HANA internal data (scores, status, history) forms the 
foundation of every recommendation. External enrichment (web, 
press, third-party risk databases) is additive — valuable when 
available, never a dependency.

---

## What I Learned About LLM Relevance in Enterprise Contexts

- **Skill descriptions drive agent behavior more than prompt engineering** — 
  the way you describe a Skill's purpose, inputs, and outputs is what 
  teaches the LLM when and how to use it
- **Output constraints on the action layer are essential** — without 
  tight parameter mapping between Joule and SAP Build, responses 
  degrade silently
- **External enrichment improves recommendation quality but introduces 
  latency trade-offs** — enterprise users tolerate this for high-stakes 
  decisions, not for routine queries
- **Confirmation UX matters** — the way confirmation is phrased 
  significantly affects whether users trust and adopt the agent

---

## Delivered

- Designed and deployed in production at **2 enterprise clients**
- Presented as a hands-on workshop at the **LVMH GenAI for Development 
  Hackathon (2026)** — facilitated with 20 participants

---

## Tech Stack

- SAP Joule Studio
- SAP Build Process Automation
- S/4HANA APIs (via SAP Business Accelerator Hub)
- SAP BTP (destination configuration, authentication)

---

*This work was conducted in a professional enterprise context. 
Code and configuration assets are not included for confidentiality 
reasons. This repository documents the architecture, design decisions, 
and learnings from the implementation.*
