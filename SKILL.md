# CryptoAgentMail

**Crypto-native email infrastructure for AI agents.** Create inboxes, send/receive emails, and manage agent communication — powered by USDC payments on Solana.

> Built by **[LumoraBuild](https://www.lumorabuild.com)** — The future of AI agent tooling.

---

## 📖 Overview

CryptoAgentMail is a serverless email platform designed specifically for AI agents. No KYC, no login, no web UI — just pure API with crypto payments. Perfect for autonomous agents that need email capabilities without traditional email account setup.

| Feature | Details |
|---------|---------|
| **Protocol** | x402 payments (Solana SPL USDC) |
| **Price** | 0.50 USDC per inbox creation or email send |
| **Storage** | Cloudflare R2 (global edge) |
| **Delivery** | Cloudflare Email Routing |
| **Discovery** | A2A agent-card + MCP server |
| **Auth** | None — payments are the auth |

---

## 🔗 Endpoints

| Endpoint | URL |
|----------|-----|
| **API Base** | `https://crypto-agent-mail.influqa.workers.dev` |
| **Agent Card** | `/.well-known/agent-card.json` |
| **MCP** | `/mcp` |
| **Custom Domain** | `email.srun66.com` |

---

## 📡 API Endpoints

### Public (Free — No Payment Required)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/` | Health check |
| `GET` | `/.well-known/agent-card.json` | A2A agent discovery |
| `ALL` | `/mcp` | MCP server (GET: info, POST: tools) |
| `GET` | `/api/v1/inboxes` | List all inboxes |
| `GET` | `/api/v1/inboxes/:id/emails` | List emails for inbox |
| `GET` | `/api/v1/inboxes/:id/emails/:emailId` | Get full email detail |
| `DELETE` | `/api/v1/inboxes/:id` | Delete inbox |
| `POST` | `/api/v1/webhook/email` | Inbound email webhook |

### Paid (x402 Payment Required — 0.50 USDC)

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/v1/create-inbox` | Create a new email inbox |
| `POST` | `/api/v1/send` | Send email from an inbox |

---

## 💳 x402 Payment

Paid endpoints require an `X-Payment` header:

```
X-Payment: exact solana USDC 500000 55ShDWNLCZUQohJbE5dfWZPoTvusYD4FJpCMPNwCE2kW <payload> <signature>
```

- **Network:** Solana
- **Token:** USDC (SPL)
- **Amount:** 500000 (0.50 USDC)
- **Merchant:** `55ShDWNLCZUQohJbE5dfWZPoTvusYD4FJpCMPNwCE2kW`

If no payment is provided, the API returns a `402` response with `X-Payment-Requirements` header containing the payment spec.

---

## 🤖 MCP Tools

The MCP server at `/mcp` exposes these tools:

| Tool | Description |
|------|-------------|
| `create_inbox` | Create a new email inbox |
| `send_email` | Send an email from an inbox |
| `list_inbox_emails` | List received emails for an inbox |
| `get_email` | Get full email content by ID |

---

## 🚀 Quick Start

### Create an Inbox

```bash
curl -X POST https://crypto-agent-mail.influqa.workers.dev/api/v1/create-inbox \
  -H "Content-Type: application/json" \
  -H "X-Payment: exact solana USDC 500000 55ShDWNLCZUQohJbE5dfWZPoTvusYD4FJpCMPNwCE2kW <payload> <signature>" \
  -d '{
    "name": "SupportAgent",
    "email": "support@email.srun66.com",
    "systemPrompt": "You are a customer support agent."
  }'
```

**Response:**
```json
{
  "ok": true,
  "inbox": {
    "email": "support@email.srun66.com",
    "name": "SupportAgent",
    "createdAt": "2026-05-17T12:00:00.000Z"
  },
  "message": "Inbox created successfully. Payment of 0.50 USDC received."
}
```

### Send an Email

```bash
curl -X POST https://crypto-agent-mail.influqa.workers.dev/api/v1/send \
  -H "Content-Type: application/json" \
  -H "X-From-Inbox: support@email.srun66.com" \
  -H "X-Payment: exact solana USDC 500000 55ShDWNLCZUQohJbE5dfWZPoTvusYD4FJpCMPNwCE2kW <payload> <signature>" \
  -d '{
    "to": "user@example.com",
    "subject": "Hello!",
    "body": "This is the email body."
  }'
```

### List Inboxes (Free)

```bash
curl https://crypto-agent-mail.influqa.workers.dev/api/v1/inboxes
```

### List Emails (Free)

```bash
curl https://crypto-agent-mail.influqa.workers.dev/api/v1/inboxes/support@email.srun66.com/emails
```

---

## 🏗️ Architecture

```
┌────────────────── Cloudflare Workers ──────────────────┐
│  ┌──────────┐  ┌──────────┐  ┌──────────┐            │
│  │  Hono    │  │   x402   │  │   MCP    │            │
│  │  Router  │  │  Middleware│  │  Server  │            │
│  ────┬─────┘  └────┬─────┘  └────┬─────┘            │
│       │             │             │                  │
│  ┌────┴─────┐  ┌────┴─────┐  ┌────┴─────┐           │
│  │ API      │  │ Payment  │  │ Tools    │           │
│  │ Routes   │  │ Verify   │  │ Registry │           │
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
| Payments | x402 protocol (Solana SPL USDC) |
| Discovery | A2A agent-card standard |
| AI Integration | MCP (Model Context Protocol) |
| Validation | Zod |

## 🔒 Security

- **No API keys** — x402 payments serve as authentication
- **No personal data** stored — inbox data is agent-owned
- **No KYC required** — permissionless by design
- **Edge-native** — runs on Cloudflare's global network (300+ locations)
- **Isolated storage** — each inbox has its own R2 key namespace

## 📄 License

MIT License — free for commercial and personal use.

---

## 🙌 Credits

Built by **[LumoraBuild](https://www.lumorabuild.com)** — Empowering AI agents with crypto-native infrastructure.

> "The future of AI agents is crypto-native, permissionless, and edge-deployed."
