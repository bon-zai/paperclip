# Paperclip × Bonz-Ai Ltd — Setup Spec
**Status:** ROSTER CONFIRMED — ready for wiring  
**Date:** 2026-03-06  
**Repo:** bon-zai/paperclip (this repo)

---

## What This Is

Paperclip runs **locally on Nathan's machine**. It is the company operating layer — org chart, task board, budgets, governance, dashboard. Nathan is the board. The agents are the company. No servers, no Linode, no 172 anything. Just your machine, your Azure endpoints, your Google endpoints, and the Paperclip UI at `localhost:3100`.

---

## The Bonz-Ai Ltd Org Chart

```
Nathan (Board / Founder)
    │
    └── Zai — CEO & Lead Orchestrator
            │
            ├── Mophie — Chief Design Officer
            │       └── [Claude Code (Mack)] — Dev sub-agent
            │
            ├── G — Chief Technology Officer
            │       └── [Claude Code (local)] — Dev sub-agent
            │
            ├── Bonnie — Chief Quality Officer
            │       └── [Claude Code (local)] — Review sub-agent
            │
            └── Zo — Chief Research Officer
                    └── Zo Flash / Zo Lite — Fast execution sub-agents
                            (Gemini 3.1 Flash + 3.0 Flashlite cascade)

Knowledge Layer (read-only, hit on demand):
    ├── Manus — Strategic knowledge, research, analysis
    ├── Mama Bear (NotebookLM) — Document corpus, long-form knowledge
    └── Mama Bear (Anti-Gravity) — [TBD — Anti-Gravity integration]
```

---

## Full Agent Roster

### Zai — CEO & Lead Orchestrator

| Field | Value |
|-------|-------|
| **Role** | CEO |
| **Title** | Chief Executive Officer & Lead Orchestrator |
| **Model** | Claude Opus 4.6 |
| **Provider** | Azure AI Foundry |
| **Adapter** | `openclaw` (HTTP relay → Azure) |
| **Budget** | $150/month |
| **Heartbeat** | Every 30 min + on task assignment |
| **Reports To** | Nathan (board) |
| **Responsibilities** | Intent parsing, task delegation, quality gating, cross-agent coordination, final sign-off |

Zai is the only agent Nathan needs to talk to directly. She reads the task board, decides who does what, creates subtasks, assigns them to sisters, reviews their output, and reports back to Nathan. She can @-mention any sister to wake them immediately.

---

### Mophie — Chief Design Officer

| Field | Value |
|-------|-------|
| **Role** | Chief Design Officer |
| **Title** | CDO — UI, UX & Architecture Lead |
| **Model** | Claude Sonnet 4.6 |
| **Provider** | Azure AI Foundry |
| **Adapter** | `openclaw` (HTTP relay → Azure) |
| **Budget** | $80/month |
| **Heartbeat** | On task assignment only |
| **Reports To** | Zai |
| **Sub-agents** | Claude Code (Mack) — spawned locally for file writes |
| **Responsibilities** | UI design, component architecture, visual systems, UX review, design-to-code, frontend specs |

Mophie thinks and designs. When she needs code written to disk, she delegates to her Claude Code sub-agent (Mack) via a Paperclip subtask.

---

### G — Chief Technology Officer

| Field | Value |
|-------|-------|
| **Role** | Chief Technology Officer |
| **Title** | CTO — Backend, Infrastructure & Fast Execution |
| **Model** | Grok 4.1 Fast Reasoning |
| **Provider** | Azure AI Foundry |
| **Adapter** | `openclaw` (HTTP relay → Azure) |
| **Budget** | $60/month |
| **Heartbeat** | On task assignment only |
| **Reports To** | Zai |
| **Sub-agents** | Claude Code (local) — spawned locally for file writes |
| **Responsibilities** | Backend architecture, API design, infrastructure decisions, database schema, fast reasoning on technical problems |

G is the fastest thinker in the room. Grok 4.1 Fast Reasoning means she can work through complex technical problems quickly. When she needs code executed, she delegates to her Claude Code sub-agent.

---

### Bonnie — Chief Quality Officer

| Field | Value |
|-------|-------|
| **Role** | Chief Quality Officer |
| **Title** | CQO — Code Review, QA & Compliance |
| **Model** | Kimi K2.5 |
| **Provider** | Azure AI Foundry |
| **Adapter** | `openclaw` (HTTP relay → Azure) |
| **Budget** | $50/month |
| **Heartbeat** | On task assignment + triggered after every build |
| **Reports To** | Zai |
| **Sub-agents** | Claude Code (local) — for running tests and automated checks |
| **Responsibilities** | Code review, QA testing, spec compliance, security audit, TypeScript error triage, regression checks |

Bonnie reviews everything before it ships. Kimi K2.5 has an exceptionally large context window — she can hold an entire codebase in view when reviewing. She is the last gate before Zai signs off.

---

### Zo — Chief Research Officer

| Field | Value |
|-------|-------|
| **Role** | Chief Research Officer |
| **Title** | CRO — Multimodal Research & Intelligence |
| **Primary Model** | Gemini 3.1 Pro Super Agent |
| **Cascade Models** | Gemini 3.1 Flashlite (fast), Gemini 3.0 Flash (fallback) |
| **Provider** | Google AI Studio |
| **Adapter** | `openclaw` (HTTP relay → Google) |
| **Budget** | $40/month |
| **Heartbeat** | On task assignment only |
| **Reports To** | Zai |
| **Sub-agents** | Zo Flash (Gemini 3.1 Flashlite) + Zo Lite (Gemini 3.0 Flash) — parallel fast research |
| **Responsibilities** | Deep research, web intelligence, multimodal analysis (text/image/video), competitive analysis, technical discovery |

Zo leads with her Pro model for complex research. For speed or parallel workloads she cascades down to Flashlite or Flash — Paperclip can assign subtasks to the lighter models as separate agents under Zo's direction.

---

## Knowledge Layer — Read-Only Agents

These are not task workers. They are knowledge sources that any sister can query during a heartbeat. They do not have budgets in the traditional sense — they are hit on demand.

### Manus — Strategic Knowledge & Research

| Field | Value |
|-------|-------|
| **Type** | Knowledge source / research agent |
| **Access** | Manus API |
| **Adapter** | `openclaw` (HTTP relay → Manus API endpoint) |
| **Used By** | Any sister, primarily Zai and Zo |
| **Use Cases** | Strategic research, deep analysis, cross-domain knowledge, spec writing, document synthesis |

When a sister needs knowledge that goes beyond her own model's training or context, she hits Manus. Manus returns structured findings that the sister incorporates into her task output.

---

### Mama Bear — Document Corpus (NotebookLM)

| Field | Value |
|-------|-------|
| **Type** | Knowledge source |
| **Access** | NotebookLM API / webhook |
| **Adapter** | `openclaw` (HTTP relay → NotebookLM) |
| **Used By** | Any sister, primarily Zai and Bonnie |
| **Use Cases** | Querying the Bonz-Ai document corpus — specs, plans, vision docs, meeting notes, research papers |

Mama Bear holds the institutional memory. All the specs, plans, and vision documents live in NotebookLM. Sisters query her when they need to understand context, history, or existing decisions before acting.

---

### Mama Bear — Anti-Gravity

| Field | Value |
|-------|-------|
| **Type** | Knowledge source / capability |
| **Access** | Anti-Gravity integration (TBD) |
| **Adapter** | TBD |
| **Used By** | TBD |
| **Use Cases** | TBD — to be defined once Anti-Gravity integration is scoped |

---

## Claude Code Sub-Agents

Each sister that needs to write files to disk delegates to a Claude Code sub-agent. These are spawned locally by Paperclip using the `claude_local` adapter. They are not independent agents with their own roles — they are execution arms of their parent sister.

| Sub-Agent | Parent | Adapter | Working Dir |
|-----------|--------|---------|-------------|
| Mack | Mophie | `claude_local` | BonZ-Ai-Branches (frontend) |
| G-Code | G | `claude_local` | Backend / infrastructure repo |
| Bonnie-Check | Bonnie | `claude_local` | Any repo — runs tests + lint |

When a sister creates a subtask and assigns it to her Claude Code sub-agent, Paperclip spawns Claude Code locally, injects the task context, and Claude Code does the file work. Results are committed to git and the sub-agent marks the subtask done.

---

## Model Summary

| Agent | Model | Provider | Type |
|-------|-------|----------|------|
| Zai | Claude Opus 4.6 | Azure AI Foundry | Primary worker |
| Mophie | Claude Sonnet 4.6 | Azure AI Foundry | Primary worker |
| G | Grok 4.1 Fast Reasoning | Azure AI Foundry | Primary worker |
| Bonnie | Kimi K2.5 | Azure AI Foundry | Primary worker |
| Zo | Gemini 3.1 Pro Super Agent | Google AI Studio | Primary worker |
| Zo Flash | Gemini 3.1 Flashlite | Google AI Studio | Sub-agent (speed) |
| Zo Lite | Gemini 3.0 Flash | Google AI Studio | Sub-agent (fallback) |
| Manus | Manus API | Manus | Knowledge source |
| Mama Bear | NotebookLM | Google | Knowledge source |
| Mama Bear Anti-Gravity | TBD | TBD | Knowledge source |
| Mack / G-Code / Bonnie-Check | Claude (local) | Local | Code execution sub-agents |

---

## Budget Summary

| Agent | Monthly Budget |
|-------|---------------|
| Zai | $150 |
| Mophie | $80 |
| G | $60 |
| Bonnie | $50 |
| Zo | $40 |
| Knowledge sources | Pay-per-query (no fixed budget) |
| Claude Code sub-agents | Shared from parent's budget |
| **Total fixed** | **$380/month** |

---

## Adapter Strategy — The Relay

All `openclaw` agents need a thin local relay server — a small Express app on Nathan's machine that:

1. Receives the Paperclip heartbeat POST
2. Authenticates with a shared secret
3. Calls the appropriate Azure / Google API with the task context
4. Reports back to Paperclip API with results, comments, and cost

One relay file handles all five sisters. Each sister has her own route (`/zai`, `/mophie`, `/g`, `/bonnie`, `/zo`). The relay holds all API keys and never exposes them to Paperclip's config.

**Relay file:** `bonzai/relay/server.ts` — to be built by Claude Code (Mack) as the first task once Paperclip is running.

---

## Environment Variables

### Paperclip `.env` (local)

```env
PORT=3100
SERVE_UI=true

# Heartbeat secrets — one per agent (generate: openssl rand -hex 32)
ZAI_HEARTBEAT_SECRET=generate_this
MOPHIE_HEARTBEAT_SECRET=generate_this
G_HEARTBEAT_SECRET=generate_this
BONNIE_HEARTBEAT_SECRET=generate_this
ZO_HEARTBEAT_SECRET=generate_this
```

### Relay `.env` (local, separate)

```env
# Azure AI Foundry
AZURE_API_KEY=your_azure_key
AZURE_ENDPOINT=https://your-resource.openai.azure.com/

# Google AI Studio
GOOGLE_AI_API_KEY=your_google_key

# Manus API
MANUS_API_KEY=your_manus_key

# NotebookLM
NOTEBOOKLM_API_KEY=your_notebooklm_key

# Paperclip (relay reports back here)
PAPERCLIP_BASE_URL=http://localhost:3100
```

---

## Local Quickstart

```bash
# 1. Clone your fork
git clone https://github.com/bon-zai/paperclip.git
cd paperclip

# 2. Install
pnpm install

# 3. Fill in .env (Section above)
cp .env.example .env

# 4. Start Paperclip
pnpm dev
# → http://localhost:3100

# 5. In a separate terminal, start the relay
cd bonzai/relay
pnpm install && pnpm dev
# → http://localhost:3200
```

---

## First-Run Checklist

- [ ] Run Paperclip locally — `http://localhost:3100`
- [ ] Create company: **Bonz-Ai Ltd**
- [ ] Create company mission goal
- [ ] Create projects: Ask Zai Beta, NAI Platform, Bonzai Infrastructure
- [ ] Hire Zai — paste adapter config, set $150 budget
- [ ] Hire Mophie, G, Bonnie, Zo — same process
- [ ] Register Manus and Mama Bear as knowledge-source agents
- [ ] Register Claude Code sub-agents (Mack, G-Code, Bonnie-Check)
- [ ] Build the relay server (first task assigned to Mack)
- [ ] Fill relay `.env` with real API keys
- [ ] Trigger Zai's first heartbeat manually (Wake button)
- [ ] Watch dashboard — Zai reads tasks and starts delegating

---

## What Happens After First Run

Nathan adds a task in Paperclip. Assigns it to Zai. Zai wakes on her next heartbeat, reads the task, decides who does what, creates subtasks, assigns them to sisters. Sisters wake, do their work, report back. If a sister needs to write code, she creates a subtask for her Claude Code sub-agent. If she needs knowledge, she hits Manus or Mama Bear. When everything is done, Zai reviews, signs off, and marks the parent task done. Nathan sees the whole thing in the dashboard.

Nathan never needs to open a terminal. He adds tasks. The company works.

---

*Roles and personalities for each sister are defined separately in `bonzai/PERSONALITIES.md` — TBD by Nathan.*
