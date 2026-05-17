# CryptoAgentMail

**Credit-based email infrastructure for AI agents.** Create inboxes, send/receive emails, persistent memory, and inbox recovery — powered by Solana USDC credits.

> Version **0.4.0** · Built by **[CryptoAgentMail](https://www.srun66.com)**

---

## 📖 Overview

CryptoAgentMail is a serverless email platform designed for AI agents. Buy credits once, use forever — no subscriptions required. Pay via Solana USDC, authenticate with an API key.

| Feature | Details |
|---------|---------|
| **Protocol** | MCP (Model Context Protocol) |
| **Auth** | API key (`X-API-Key` header) |
| **Currency** | USDC on Solana |
| **Storage** | Cloudflare R2 (global edge) |
| **Delivery** | Cloudflare Email Routing |
| **Discovery** | A2A agent-card |

---

## 🔗 Endpoints

| Endpoint | URL |
|----------|-----|
| **Website** | `https://www.srun66.com` |
| **API Base** | `https://srun66.com` |
| **MCP Server** | `https://srun66.com/mcp` |
| **Agent Card** | `https://srun66.com/.well-known/agent-card.json` |

---

## 💳 Credits & Pricing

**1 Credit = $1 USDC** (Solana SPL)

| Action | Cost |
|--------|------|
| Send email | 0.1 credits |
| Create inbox | 1 credit |
| Agent memory | 0.05 credits/day |
| Receive email | FREE |

### Credit Packages

| Package | Credits | Price |
|---------|---------|-------|
| Starter | 5 | 5 USDC |
| Pro | 25 | 25 USDC |
| Enterprise | 100 | 100 USDC |

### Monthly Plans (Recurring)

| Plan | Price | Emails/Mo | Inboxes |
|------|-------|-----------|---------|
| Starter | $12/mo | 10,000 | 1 |
| Pro | $24/mo | 25,000 | 3 |
| Enterprise | $50/mo | 100,000 | Unlimited |

---

## 🔐 Authentication

All API calls require an API key in the header:

```
X-API-Key: your-api-key-here
```

Get your API key from your account after purchasing credits.

---

## 🤖 MCP Tools

The MCP server at `https://srun66.com/mcp` exposes these tools:

| Tool | Description | Cost |
|------|-------------|------|
| `create_inbox` | Create a new email inbox | 1 credit |
| `send_email` | Send an email from an inbox | 0.1 credits |
| `list_inbox_emails` | List received emails | FREE |
| `get_email` | Get full email content | FREE |
| `store_memory` | Persist agent memory | 0.05 credits/day |
| `recover_inbox` | Recover inbox to new agent | FREE |

---

## 🚀 Quick Start

### 1. Get API Key

Purchase credits at `https://www.srun66.com` and receive your API key.

### 2. Create an Inbox

```bash
curl -X POST https://srun66.com/api/v1/create-inbox \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your-api-key-here" \
  -d '{
    "name": "SupportAgent",
    "email": "support@agent.srun66.com",
    "systemPrompt": "You are a customer support agent."
  }'
```

### 3. Send an Email

```bash
curl -X POST https://srun66.com/api/v1/send \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your-api-key-here" \
  -H "X-From-Inbox: support@agent.srun66.com" \
  -d '{
    "to": "user@example.com",
    "subject": "Hello!",
    "body": "This is the email body."
  }'
```

### 4. List Inboxes (Free)

```bash
curl -H "X-API-Key: your-api-key-here" \
  https://srun66.com/api/v1/inboxes
```

### 5. List Emails (Free)

```bash
curl -H "X-API-Key: your-api-key-here" \
  https://srun66.com/api/v1/inboxes/support@agent.srun66.com/emails
```

---

## 🏗️ Architecture

```
┌────────────────── Cloudflare Workers ──────────────────┐
│  ┌──────────┐  ┌──────────┐  ┌──────────┐            │
│  │  Hono    │  │  Credit  │  │   MCP    │            │
│  │  Router  │  │  System  │  │  Server  │            │
│  ────┬─────┘  └────┬─────┘  └────┬─────┘            │
│       │             │             │                  │
│  ┌────┴─────┐  ┌────┴─────┐  ┌────┴─────┐           │
│  │ API      │  │ Credit   │  │ Tools    │           │
│  │ Routes   │  │ Deduct   │  │ Registry │           │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘           │
└───────┼─────────────┼─────────────┼──────────────────┘
        │             │             │
   ┌────▼─────┐  ┌────▼─────┐  ┌────▼─────┐
   │ R2       │  │ Solana   │  │ MCP      │
   │ Storage  │  │ USDC     │  │ Clients  │
   └──────────┘  └──────────┘  └──────────┘
```

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Runtime | Cloudflare Workers (V8 isolates) |
| Framework | Hono |
| Storage | Cloudflare R2 |
| Email | Cloudflare Email Routing + Send Email binding |
| Payments | Solana SPL USDC |
| Auth | API key |
| Discovery | A2A agent-card |
| AI Integration | MCP (Model Context Protocol) |
| Validation | Zod |

## 🔒 Security

- **API key auth** — each request authenticated via `X-API-Key`
- **Credit-based billing** — transparent, per-action costs
- **No personal data** stored — inbox data is agent-owned
- **Edge-native** — runs on Cloudflare's global network (300+ locations)
- **Isolated storage** — each inbox has its own R2 key namespace
- **Inbox recovery** — if agent crashes, authorize a new agent to access the same inbox

## 📄 License

MIT License — free for commercial and personal use.

---

## 🙌 Credits

Built by **[CryptoAgentMail](https://www.srun66.com)** — Empowering AI agents with crypto-native email infrastructure.

> "Buy credits once, use forever. No subscriptions. No hidden fees."
