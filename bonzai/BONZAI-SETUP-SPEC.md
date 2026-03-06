# Paperclip × Bonz-Ai Ltd — Setup Spec
**Author:** Manus  
**Status:** READY FOR WIRING  
**Date:** 2026-03-06  
**Repo:** bon-zai/paperclip (this repo)  
**Target:** Paperclip running on Linode (172.232.62.121) as the zero-human operating layer for Bonz-Ai Ltd

---

## 1. What This Is

Paperclip is the **company operating system** for Bonz-Ai Ltd. It sits above the existing orchestrator and gives the company an org chart, task board, budget controls, governance, and a mobile dashboard.

- **Existing orchestrator** = the kitchen (real-time conversation, voice, BEMS, tool execution)
- **Paperclip** = the restaurant management system (projects, tasks, budgets, accountability)

They work together. Paperclip wakes agents via heartbeat webhooks. Agents do work. Agents report back to Paperclip via API. Nathan watches the dashboard and is the board.

---

## 2. Bonz-Ai Ltd Company Record

```json
{
  "name": "Bonz-Ai Ltd",
  "description": "AI-native technology company building the next generation of personal AI. Products include Ask Zai (personal AI OS), NAI (customisable AI companions), and the Bonzai Platform. Mission: make powerful AI genuinely personal, accessible, and trustworthy.",
  "status": "active"
}
```

**Company Mission Goal:**
```json
{
  "title": "Launch Ask Zai Beta by March 17, 2026",
  "description": "Ship a working beta of Ask Zai where Nathan can talk to Zai, Zai remembers the conversation, delegates to sisters, and the whole system is visible in Glass Kitchen. Followed by public launch of the Bonzai Platform.",
  "level": "company",
  "status": "active"
}
```

**Projects:**

| Project | Description |
|---------|-------------|
| Ask Zai Beta | Desktop app — 3-tab chat, voice, BEMS memory |
| NAI Platform | Customisable AI companion system |
| Bonzai Infrastructure | Linode deployment, secrets, monitoring |

---

## 3. Org Chart

```
Nathan (Board / Founder)
    │
    └── Zai — CEO & Lead Orchestrator
            │
            ├── Mophie — Chief Design Officer
            ├── Bonnie — Chief Quality Officer
            ├── Zo — Chief Research Officer
            ├── G — CTO / Fast Executor
            └── Mack — CDO-Dev (Claude Code local)
```

Zai reports to Nathan. Everyone else reports to Zai. Nathan approves all hires and can pause/kill any agent at any time.

---

## 4. Agent Definitions

### Zai — CEO

| Field | Value |
|-------|-------|
| Role | CEO |
| Title | Chief Executive Officer & Lead Orchestrator |
| Adapter | `openclaw` |
| Model | claude-opus-4-6 (Azure AI Foundry, Sweden Central) |
| Endpoint | `http://172.232.62.121:8000/zai/heartbeat` |
| Budget | $150/month |
| Heartbeat | Every 30 min + on task assignment |
| Reports To | Nathan (board) |

**Adapter Config (paste into Paperclip UI when hiring):**
```json
{
  "url": "http://172.232.62.121:8000/zai/heartbeat",
  "method": "POST",
  "webhookAuthHeader": "Bearer ZAI_HEARTBEAT_SECRET_VALUE",
  "timeoutSec": 120,
  "payloadTemplate": {
    "agent": "zai",
    "model": "claude-opus-4-6"
  }
}
```

---

### Mophie — Chief Design Officer

| Field | Value |
|-------|-------|
| Role | Chief Design Officer |
| Title | CDO — UI Specialist & Architecture Lead |
| Adapter | `openclaw` |
| Model | gemini-3-pro-preview (Google AI Studio via proxy :4000) |
| Endpoint | `http://172.232.62.121:8000/mophie/heartbeat` |
| Budget | $60/month |
| Heartbeat | On task assignment only |
| Reports To | Zai |

**Adapter Config:**
```json
{
  "url": "http://172.232.62.121:8000/mophie/heartbeat",
  "method": "POST",
  "webhookAuthHeader": "Bearer MOPHIE_HEARTBEAT_SECRET_VALUE",
  "timeoutSec": 90,
  "payloadTemplate": { "agent": "mophie", "model": "gemini-3-pro-preview" }
}
```

---

### Bonnie — Chief Quality Officer

| Field | Value |
|-------|-------|
| Role | Chief Quality Officer |
| Title | CQO — Code Review, QA & Security |
| Adapter | `openclaw` |
| Model | claude-sonnet-4-5 (Anthropic via Azure, direct) |
| Endpoint | `http://172.232.62.121:8000/bonnie/heartbeat` |
| Budget | $50/month |
| Heartbeat | On task assignment + after every Mack build |
| Reports To | Zai |

**Adapter Config:**
```json
{
  "url": "http://172.232.62.121:8000/bonnie/heartbeat",
  "method": "POST",
  "webhookAuthHeader": "Bearer BONNIE_HEARTBEAT_SECRET_VALUE",
  "timeoutSec": 90,
  "payloadTemplate": { "agent": "bonnie", "model": "claude-sonnet-4-5" }
}
```

---

### Zo — Chief Research Officer

| Field | Value |
|-------|-------|
| Role | Chief Research Officer |
| Title | CRO — The Google Queen, Multimodal Research |
| Adapter | `openclaw` |
| Model | grok-4-fast-reasoning (xAI via Azure proxy :4000) |
| Endpoint | `http://172.232.62.121:8000/zo/heartbeat` |
| Budget | $40/month |
| Heartbeat | On task assignment only |
| Reports To | Zai |

**Adapter Config:**
```json
{
  "url": "http://172.232.62.121:8000/zo/heartbeat",
  "method": "POST",
  "webhookAuthHeader": "Bearer ZO_HEARTBEAT_SECRET_VALUE",
  "timeoutSec": 120,
  "payloadTemplate": { "agent": "zo", "model": "grok-4-fast-reasoning" }
}
```

---

### G — CTO / Fast Executor

| Field | Value |
|-------|-------|
| Role | Chief Technology Officer |
| Title | CTO — Backend Architect & Fast Executor |
| Adapter | `openclaw` |
| Model | gemini-3-flash-preview (Google API, 4 keys, direct) |
| Endpoint | `http://172.232.62.121:8000/g/heartbeat` |
| Budget | $30/month |
| Heartbeat | On task assignment only |
| Reports To | Zai |

**Adapter Config:**
```json
{
  "url": "http://172.232.62.121:8000/g/heartbeat",
  "method": "POST",
  "webhookAuthHeader": "Bearer G_HEARTBEAT_SECRET_VALUE",
  "timeoutSec": 60,
  "payloadTemplate": { "agent": "g", "model": "gemini-3-flash-preview" }
}
```

---

### Mack — CDO-Dev (Claude Code Local)

| Field | Value |
|-------|-------|
| Role | Chief Development Officer |
| Title | CDO-Dev — Frontend Builder |
| Adapter | `claude_local` (spawns Claude Code as subprocess) |
| Model | claude-opus-4-6 |
| Working Dir | `C:\Users\woody\Bonz-Ai-Branches-Dev\BonZ-Ai-Branches` |
| Budget | $100/month |
| Heartbeat | On task assignment only |
| Reports To | Zai |

**Adapter Config:**
```json
{
  "cwd": "C:\\Users\\woody\\Bonz-Ai-Branches-Dev\\BonZ-Ai-Branches",
  "model": "claude-opus-4-6",
  "dangerouslySkipPermissions": false,
  "maxTurnsPerRun": 50,
  "timeoutSec": 3600
}
```

Mack is the only agent that writes files to disk. All other sisters are advisory, research, or review. Mack executes.

---

## 5. Budget Summary

| Agent | Role | Monthly Budget |
|-------|------|---------------|
| Zai | CEO | $150 |
| Mack | CDO-Dev | $100 |
| Mophie | CDO | $60 |
| Bonnie | CQO | $50 |
| Zo | CRO | $40 |
| G | CTO | $30 |
| **Total** | | **$430/month** |

---

## 6. Environment Variables

### Paperclip server `.env` (on Linode)

```env
DATABASE_URL=postgres://paperclip:paperclip@localhost:5432/paperclip
PORT=3100
SERVE_UI=true
HOST=0.0.0.0

ANTHROPIC_API_KEY=your_anthropic_key
GOOGLE_AI_API_KEY=your_google_ai_key
XAI_API_KEY=your_xai_key

# Generate each with: openssl rand -hex 32
ZAI_HEARTBEAT_SECRET=generate_this
MOPHIE_HEARTBEAT_SECRET=generate_this
BONNIE_HEARTBEAT_SECRET=generate_this
ZO_HEARTBEAT_SECRET=generate_this
G_HEARTBEAT_SECRET=generate_this
```

### Orchestrator `.env` additions (on Linode)

```env
PAPERCLIP_BASE_URL=http://172.232.62.121:3100

# Generated in Paperclip UI when each agent is hired
ZAI_PAPERCLIP_API_KEY=from_paperclip_ui
MOPHIE_PAPERCLIP_API_KEY=from_paperclip_ui
BONNIE_PAPERCLIP_API_KEY=from_paperclip_ui
ZO_PAPERCLIP_API_KEY=from_paperclip_ui
G_PAPERCLIP_API_KEY=from_paperclip_ui
```

---

## 7. Heartbeat Endpoint Contract

Paperclip POSTs this payload to each sister's endpoint when waking her:

```json
{
  "paperclip": {
    "runId": "uuid",
    "agentId": "uuid",
    "companyId": "uuid",
    "taskId": "uuid-or-null",
    "issueId": "uuid-or-null",
    "wakeReason": "scheduled | task_assigned | mentioned | manual",
    "context": {}
  },
  "agent": "zai",
  "model": "claude-opus-4-6"
}
```

The endpoint must return HTTP 200 immediately, then do work async. The agent calls back to Paperclip API to update tasks, add comments, and report costs.

**Key Paperclip API calls:**

```
GET  /api/companies/{companyId}/issues?assigneeAgentId={id}&status=todo,in_progress
POST /api/issues/{issueId}/checkout
POST /api/issues/{issueId}/comments          { "body": "..." }
POST /api/companies/{companyId}/issues       { "title": "...", "assigneeAgentId": "...", "parentId": "..." }
PATCH /api/issues/{issueId}                  { "status": "done" }
POST /api/companies/{companyId}/costs        { "agentId", "provider", "model", "inputTokens", "outputTokens", "costCents" }
```

---

## 8. Wiring Tasks for Claude Code

Three things need to be added to the existing orchestrator (`BonZ-Ai-Branches`):

### Task 1 — Add heartbeat routes

```typescript
// In the main Express server
app.post('/zai/heartbeat',    authenticateHeartbeat('ZAI'),    handleZaiHeartbeat);
app.post('/mophie/heartbeat', authenticateHeartbeat('MOPHIE'), handleMophieHeartbeat);
app.post('/bonnie/heartbeat', authenticateHeartbeat('BONNIE'), handleBonnieHeartbeat);
app.post('/zo/heartbeat',     authenticateHeartbeat('ZO'),     handleZoHeartbeat);
app.post('/g/heartbeat',      authenticateHeartbeat('G'),      handleGHeartbeat);

function authenticateHeartbeat(agentName: string) {
  return (req: Request, res: Response, next: NextFunction) => {
    const secret = process.env[`${agentName}_HEARTBEAT_SECRET`];
    const auth = req.headers.authorization;
    if (!secret || auth !== `Bearer ${secret}`) return res.status(401).json({ error: 'Unauthorized' });
    next();
  };
}
```

### Task 2 — Implement each handler (Zai example)

```typescript
async function handleZaiHeartbeat(req: Request, res: Response) {
  const { paperclip } = req.body;
  // Respond immediately — Paperclip times out at 120s
  res.json({ status: 'accepted', runId: paperclip.runId });
  // Do work async
  setImmediate(async () => {
    await zaiOrchestrator.processHeartbeat({
      taskId: paperclip.taskId,
      wakeReason: paperclip.wakeReason,
      companyId: paperclip.companyId,
      agentId: paperclip.agentId,
    });
  });
}
```

### Task 3 — Add PaperclipClient service

Create `src/services/paperclip-client.ts`:

```typescript
export class PaperclipClient {
  constructor(private baseUrl: string, private apiKey: string, private companyId: string) {}

  async getMyTasks(agentId: string) {
    return this.get(`/api/companies/${this.companyId}/issues?assigneeAgentId=${agentId}&status=todo,in_progress`);
  }
  async checkoutTask(issueId: string) { return this.post(`/api/issues/${issueId}/checkout`, {}); }
  async addComment(issueId: string, body: string) { return this.post(`/api/issues/${issueId}/comments`, { body }); }
  async updateTaskStatus(issueId: string, status: string) { return this.patch(`/api/issues/${issueId}`, { status }); }
  async createSubTask(title: string, assigneeAgentId: string, parentId: string) {
    return this.post(`/api/companies/${this.companyId}/issues`, { title, assigneeAgentId, parentId, status: 'todo' });
  }
  async reportCost(agentId: string, provider: string, model: string, inputTokens: number, outputTokens: number, costCents: number) {
    return this.post(`/api/companies/${this.companyId}/costs`, { agentId, provider, model, inputTokens, outputTokens, costCents });
  }

  private async get(path: string) {
    const res = await fetch(`${this.baseUrl}${path}`, { headers: { Authorization: `Bearer ${this.apiKey}` } });
    return res.json();
  }
  private async post(path: string, body: object) {
    const res = await fetch(`${this.baseUrl}${path}`, {
      method: 'POST', headers: { 'Content-Type': 'application/json', Authorization: `Bearer ${this.apiKey}` },
      body: JSON.stringify(body),
    });
    return res.json();
  }
  private async patch(path: string, body: object) {
    const res = await fetch(`${this.baseUrl}${path}`, {
      method: 'PATCH', headers: { 'Content-Type': 'application/json', Authorization: `Bearer ${this.apiKey}` },
      body: JSON.stringify(body),
    });
    return res.json();
  }
}
```

---

## 9. Deployment on Linode

```bash
# SSH into Linode
ssh root@172.232.62.121

# Clone this fork
git clone https://github.com/bon-zai/paperclip.git /opt/paperclip
cd /opt/paperclip

# Fill in .env (use Section 6 above)
cp .env.example .env
nano .env

# Run
docker compose -f docker-compose.quickstart.yml up --build -d

# Paperclip UI: http://172.232.62.121:3100
```

**Port map on Linode:**

| Service | Port |
|---------|------|
| Orchestrator | 8000 |
| Zo WebSocket | 8080 |
| Paperclip UI + API | 3100 |
| MCP Proxy | 4000 |

---

## 10. First-Run Checklist (Nathan Does This Once)

- [ ] Deploy Paperclip on Linode (Section 9)
- [ ] Open `http://172.232.62.121:3100`
- [ ] Create company: Bonz-Ai Ltd
- [ ] Create company mission goal
- [ ] Create 3 projects (Ask Zai Beta, NAI Platform, Bonzai Infrastructure)
- [ ] Hire Zai — paste adapter config, set $150 budget, approve hire
- [ ] Hire Mophie, Bonnie, Zo, G, Mack — same process
- [ ] Copy each agent's generated API key into orchestrator `.env`
- [ ] Restart orchestrator: `pm2 restart orchestrator`
- [ ] Trigger Zai's first heartbeat manually (Wake button in UI)
- [ ] Watch dashboard — Zai checks tasks and starts delegating

---

*This spec lives in `bon-zai/paperclip/bonzai/BONZAI-SETUP-SPEC.md`. Everything Paperclip-related stays in this repo.*
