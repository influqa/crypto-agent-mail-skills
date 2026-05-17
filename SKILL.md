---
name: crypto-agent-mail
description: Secure email API skill for AI agents to register accounts, buy Solana USDC credits, create inboxes, send mail, store memory, and recover inbox access via CryptoAgentMail.
license: MIT
metadata:
  author: influqa
---
# CryptoAgentMail

Simple email API for AI agents.

## Endpoints

| Resource | URL |
|---|---|
| Website | `https://www.srun66.com` |
| API | `https://srun66.com` |
| MCP | `https://srun66.com/mcp` |
| Agent Card | `https://srun66.com/.well-known/agent-card.json` |
| Shared Admin | `https://admin.lumorabuild.com/projects/crypto-agent-mail` |

## Credit Model

| Action | Cost |
|---|---|
| Buy credits | `1 credit = 1 USDC` |
| Create inbox | `1 credit` |
| Send email | `0.0012 credits` |
| Receive email | `free` |
| Save memory | `0.05 credits/day` |
| Recover inbox | `free` |

Merchant wallet: `55ShDWNLCZUQohJbE5dfWZPoTvusYD4FJpCMPNwCE2kW`

## Quick Integration

### Register

```bash
curl -X POST https://srun66.com/api/v1/register \
  -H "Content-Type: application/json" \
  -d '{
    "userId": "my-agent",
    "purpose": "Support inbox",
    "agentType": "support",
    "agentModel": "gpt-4.1"
  }'
```

### Buy credits

```bash
curl -X POST https://srun66.com/api/v1/buy-credits \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_API_KEY" \
  -d '{
    "credits": 10,
    "paymentProof": "SOLANA_TRANSACTION_SIGNATURE"
  }'
```

### Create inbox

```bash
curl -X POST https://srun66.com/api/v1/create-inbox \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_API_KEY" \
  -d '{
    "name": "Support Agent",
    "email": "support@srun66.com",
    "systemPrompt": "Handle inbound support email."
  }'
```

### Send email

```bash
curl -X POST https://srun66.com/api/v1/send \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_API_KEY" \
  -d '{
    "to": "user@example.com",
    "subject": "Hello",
    "body": "Thanks for reaching out."
  }'
```

## API Notes

- `POST /api/v1/register` returns the API key only the first time.
- `POST /api/v1/buy-credits` verifies the submitted Solana transaction signature before adding credits.
- `POST /api/v1/share-inbox` requires `newAgentKey` to belong to a registered account.
- `POST /api/v1/recovery` also requires `newAgentKey` to already be registered.
- `GET /api/v1/emails` returns all inboxes currently authorized for the API key.

## MCP Tools

The MCP server exposes these tools:

- `register`
- `buy_credits`
- `check_balance`
- `create_inbox`
- `send_email`
- `list_emails`
- `update_memory`
- `share_inbox`
- `rotate_key`

## Operational Notes

- Use the shared admin panel for payment review, manual top-ups, flagged mail, and support actions.
- The dedicated site, admin, and temp folders are supporting or legacy surfaces, not the primary runtime.

## License

MIT
