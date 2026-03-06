# Paperclip × Bonz-Ai Ltd — Setup Spec
**Status:** IN PROGRESS — roles and personalities TBD  
**Date:** 2026-03-06  
**Repo:** bon-zai/paperclip (this repo)

---

## What This Is

Paperclip is an open-source application that runs **locally on Nathan's machine**. It provides an org chart, task board, budget controls, and a dashboard for managing a team of AI agents. Nathan is the board. The agents are LLMs connected via their cloud APIs.

This is not a server deployment. This is not connected to the Linode instance. This runs on your Windows machine, connects to your Azure and Google model endpoints, and gives you a company operating layer on top of them.

---

## How It Works

1. **Run Paperclip locally** — `pnpm install && pnpm dev` — UI at `http://localhost:3100`
2. **Create Bonz-Ai Ltd** — one company, one mission goal
3. **Hire agents** — each agent gets a role, a model endpoint, and a monthly budget
4. **Paperclip sends heartbeats** — on a schedule or when a task is assigned, Paperclip wakes an agent by calling its adapter
5. **Agents do work and report back** — via the Paperclip API at `localhost:3100`
6. **Nathan watches the dashboard** — every task, every cost, every decision logged

---

## Adapter Strategy

Paperclip has built-in adapters for Claude Code (`claude_local`), Codex (`codex_local`), and Cursor (`cursor_local`) — these spawn local processes. For connecting directly to Azure and Google model APIs, the right adapter is **`openclaw`** — it sends an HTTP POST to any endpoint you specify.

Since the models live at Azure and Google (not locally), the flow is:

```
Paperclip (localhost:3100)
    │
    │  heartbeat POST
    ▼
Local relay endpoint (small Express server, also on your machine)
    │
    │  API call with your Azure / Google credentials
    ▼
Azure AI Foundry / Google AI Studio
    │
    │  model response
    ▼
Relay reports back to Paperclip API
```

The relay is a thin local server — one file, one route per agent — that Paperclip can wake via HTTP. It holds your API keys and makes the actual model calls. This keeps credentials out of Paperclip's config and gives you full control over each model call.

Alternatively, if you want zero extra code, you can use `claude_local` for Claude-backed agents (Paperclip spawns Claude Code directly) and build the Google agents as `openclaw` webhooks to a minimal relay.

---

## Company Setup

**Company name:** Bonz-Ai Ltd  
**Mission:** TBD by Nathan — this is where you define what the company is working toward right now.

---

## Agents — Roles and Models TBD

The following is a placeholder structure. **Nathan decides the final roles, personalities, and which model goes where.** The table below shows the available models and the adapter type each would use.

| Agent Slot | Model Options | Adapter | Notes |
|------------|--------------|---------|-------|
| Agent 1 | Claude (Azure AI Foundry) | `claude_local` or `openclaw` relay | Best for orchestration, reasoning, writing |
| Agent 2 | Claude (Azure AI Foundry) | `claude_local` or `openclaw` relay | Second Claude instance — different role |
| Agent 3 | Gemini (Google AI Studio) | `openclaw` relay | Multimodal, fast, research |
| Agent 4 | Gemini (Google AI Studio) | `openclaw` relay | Second Gemini — different specialisation |
| Agent 5 | Claude or Gemini | TBD | Depends on what roles Nathan wants filled |

**What Nathan needs to decide:**
- How many agents to start with (can add more later)
- What role each agent plays — CEO, CTO, researcher, builder, reviewer, etc.
- Whether to give them personalities or keep them purely functional
- Monthly budget per agent (Paperclip hard-stops them when they hit it)

---

## Environment Variables (Local)

Create a `.env` file in the Paperclip repo root:

```env
# Paperclip runs embedded DB by default — no DATABASE_URL needed for local dev
PORT=3100
SERVE_UI=true

# Azure AI Foundry (for Claude models)
AZURE_OPENAI_API_KEY=your_azure_key
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com/

# Google AI Studio (for Gemini models)
GOOGLE_AI_API_KEY=your_google_key

# Anthropic direct (optional — if not using Azure)
ANTHROPIC_API_KEY=your_anthropic_key
```

---

## Local Quickstart

```bash
# Clone your fork
git clone https://github.com/bon-zai/paperclip.git
cd paperclip

# Install
pnpm install

# Start (no DATABASE_URL needed — uses embedded PGlite)
pnpm dev

# Open
http://localhost:3100
```

No Docker needed for local dev. Paperclip uses an embedded database by default — just run it and it works.

---

## What Happens Next

1. Nathan runs Paperclip locally and creates Bonz-Ai Ltd
2. Nathan decides on roles — how many agents, what they do, what to call them
3. We wire the Azure and Google model connections (relay or direct adapter)
4. Nathan hires the agents in the UI, sets budgets, and kicks off the first task
5. From that point on, Nathan adds tasks in Paperclip and the agents work through them

---

*Roles, personalities, and model assignments are decided by Nathan. This spec will be updated once those decisions are made.*
