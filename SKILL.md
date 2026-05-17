# CryptoAgentMail

**Credit-based email infrastructure for AI agents.** Buy credits with Solana USDC, use an API key for all requests. Credits never expire.

> Version **0.4.0** · Built by [LumoraBuild](https://www.lumorabuild.com) · [CryptoAgentMail](https://www.srun66.com) · [MCP](https://srun66.com/mcp)

---

## 📖 Overview

CryptoAgentMail is a serverless email platform built for AI agents. No KYC, no web UI — just API. Buy credits once with USDC on Solana, then use the API key for every request. Credits are deducted per action and never expire.

| Feature | Details |
|---------|---------|
| **Auth** | API key via `X-API-Key` header |
| **Currency** | USDC on Solana (SPL) |
| **Storage** | Cloudflare R2 |
| **Delivery** | Cloudflare Email Routing |
| **Protocol** | MCP + REST API |
| **Credits** | Never expire, buy once use forever |

---

## 🔗 Endpoints

| Resource | URL |
|----------|-----|
| **Website** | `https://www.srun66.com` |
| **API** | `https://srun66.com` |
| **MCP** | `https://srun66.com/mcp` |
| **Agent Card** | `https://srun66.com/.well-known/agent-card.json` |

---

## 💰 How to Pay for Credits

### Step 1: Register (get your API key)

```bash
curl -X POST https://srun66.com/api/v1/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "MyAgent",
    "email": "myagent@agent.srun66.com",
    "agentPurpose": "Customer support email handling",
    "agentType": "support",
    "agentModel": "gpt-4"
  }'
```

**Response:** Returns your `apiKey` and `userId`. Save these — the API key is your identity for all future requests.

### Step 2: Buy Credits (Solana USDC payment)

Send USDC on Solana to the merchant wallet, then submit the transaction signature:

```bash
curl -X POST https://srun66.com/api/v1/buy-credits \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "credits": 10,
    "paymentProof": "<your-solana-tx-signature>"
  }'
```

**Payment details:**
| Field | Value |
|-------|-------|
| **Network** | Solana |
| **Token** | USDC (SPL) |
| **Price** | 1 credit = 1 USDC |
| **Merchant** | `55ShDWNLCZUQohJbE5dfWZPoTvusYD4FJpCMPNwCE2kW` |
| **Verification** | On-chain via Solana RPC |

**Credit packages:**
| Package | Credits | Price |
|---------|---------|-------|
| Starter | 5 | 5 USDC |
| Pro | 25 | 25 USDC |
| Enterprise | 100 | 100 USDC |

### Step 3: Check Your Balance

```bash
curl -H "X-API-Key: your-api-key" https://srun66.com/api/v1/balance
```

---

## 📊 Usage Rates (Credit Deductions)

| Action | Cost (USD) | Credits (1 credit = $1) |
|--------|-----------|------------------------|
| **Send email** | $0.0012 | 0.0012 credits |
| **Create inbox** | $1.00 | 1 credit |
| **Receive email** | FREE | 0 |
| **Save agent memory** | $0.05/day | 0.05 credits/day |
| **Recover inbox** | FREE | 0 |
| **Check balance** | FREE | 0 |

**Example:** With 5 credits ($5 USDC), you can send ~4,166 emails + create 1 inbox + ~60 days of memory.

---

## 📅 Monthly Plans (Optional)

For agents that need recurring credits, monthly subscriptions are available:

| Plan | Price | Emails/Mo | Inboxes |
|------|-------|-----------|---------|
| Starter | $12/mo | 10,000 | 1 |
| Pro ⚡ | $24/mo | 25,000 | 3 |
| Enterprise | $50/mo | 100,000 | Unlimited |

Annual plans save $24–$72/year.

---

## 📡 REST API

All API requests require `X-API-Key: your-api-key` header.

### Authentication

```
X-API-Key: your-api-key-here
```

Every request is authenticated. If your key is compromised, rotate it immediately.

### Public Endpoints (No Auth)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/` | Health check |
| `GET` | `/.well-known/agent-card.json` | A2A agent discovery |
| `GET` | `/mcp` | MCP server info |
| `POST` | `/mcp` | MCP tool calls |

### Account Endpoints (Auth Required)

| Method | Path | Description | Cost |
|--------|------|-------------|------|
| `POST` | `/api/v1/register` | Register new agent, get API key | FREE |
| `GET` | `/api/v1/balance` | Check credit balance | FREE |
| `POST` | `/api/v1/rotate-key` | Rotate API key | FREE |

### Credit Endpoints (Auth + Payment Required)

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/v1/buy-credits` | Buy credits — body: `{credits, paymentProof}` |

### Inbox Endpoints (Auth Required)

| Method | Path | Description | Cost |
|--------|------|-------------|------|
| `POST` | `/api/v1/create-inbox` | Create email inbox | 1 credit |
| `POST` | `/api/v1/share-inbox` | Share inbox with another agent | FREE |
| `POST` | `/api/v1/recovery` | Request inbox recovery | FREE |

### Email Endpoints (Auth Required)

| Method | Path | Description | Cost |
|--------|------|-------------|------|
| `POST` | `/api/v1/send` | Send email from inbox | 0.0012 credits |
| `GET` | `/api/v1/emails` | List received emails (all inboxes) | FREE |

### Memory (Auth Required)

| Method | Path | Description | Cost |
|--------|------|-------------|------|
| `POST` | `/api/v1/memory` | Save/load agent memory | 0.05 credits/day |

### Email Webhook (Internal)

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/` | Inbound email webhook (Cloudflare Email Routing) |

---

## 🤖 MCP Tools

Connect to `https://srun66.com/mcp` for these tools:

| Tool | Description |
|------|-------------|
| `register_agent` | Register and get API key |
| `create_inbox` | Create a new email inbox |
| `send_email` | Send email from an inbox |
| `list_inboxes` | List your inboxes |
| `list_emails` | List received emails |
| `get_email` | Get full email content |
| `check_balance` | Check credit balance |
| `save_memory` | Save agent memory |
| `load_memory` | Load saved memory |
| `recover_inbox` | Recover inbox to new agent |
| `rotate_key` | Rotate API key |

---

## 🚀 Quick Start

### 1. Register

```bash
curl -X POST https://srun66.com/api/v1/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "SupportBot",
    "email": "support@agent.srun66.com",
    "agentPurpose": "Handle customer support emails",
    "agentType": "support",
    "agentModel": "claude"
  }'
```

Save the returned `apiKey` — you'll need it for every request.

### 2. Check Balance

```bash
curl -H "X-API-Key: YOUR_API_KEY" \
  https://srun66.com/api/v1/balance
```

### 3. Create an Inbox

```bash
curl -X POST https://srun66.com/api/v1/create-inbox \
  -H "X-API-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "support@agent.srun66.com",
    "name": "SupportBot",
    "systemPrompt": "You are a helpful customer support agent."
  }'
```

### 4. Send an Email

```bash
curl -X POST https://srun66.com/api/v1/send \
  -H "X-API-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "inboxEmail": "support@agent.srun66.com",
    "to": "user@example.com",
    "subject": "Re: Your inquiry",
    "body": "Thank you for contacting us!"
  }'
```

### 5. Check Received Emails

```bash
curl -H "X-API-Key: YOUR_API_KEY" \
  https://srun66.com/api/v1/emails
```

---

## 🔐 API Key Security

- **Never share your API key** — it's your identity and wallet
- **Rotate immediately** if compromised via `/api/v1/rotate-key`
- **Key prefix logging** — all actions log the first 8 chars of your key for audit
- **Rate limiting** — 100 requests/minute, auto-block on abuse
- **Suspension** — accounts can be suspended for policy violations

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
| Runtime | Cloudflare Workers |
| Framework | Hono + Zod validation |
| Storage | Cloudflare R2 |
| Email | Cloudflare Email Routing + Send Email |
| Payments | x402 protocol (Solana USDC) |
| Auth | API key |
| AI | MCP (Model Context Protocol) + A2A agent-card |

## 📄 License

MIT — free for commercial and personal use.

---

Built by **[CryptoAgentMail](https://www.srun66.com)** — Buy credits once, use forever.
