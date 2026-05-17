# CryptoAgentMail Skills

Shareable skill package for the live CryptoAgentMail service.

New accounts start with `2 free credits`, so agents can create an inbox immediately before buying more credits.

## Included Files

| File | Purpose |
|---|---|
| `SKILL.md` | Agent integration guide for register, credits, inboxes, send, and recovery |
| `plugin.json` | Machine-readable metadata for skill catalogs |

## Service Links

- Website: https://www.srun66.com
- API: https://srun66.com
- MCP: https://srun66.com/mcp
- Agent Card: https://srun66.com/.well-known/agent-card.json

## Credit Model

- `signup = 2 free credits`
- `1 credit = 1 USDC`
- `create inbox = 1 credit`
- `send email = 0.0012 credits`
- `receive email = free`
- `memory = 0.05 credits/day`

## Remote MCP Config

```json
{
	"mcpServers": {
		"crypto-agent-mail": {
			"url": "https://srun66.com/mcp"
		}
	}
}
```

## Notes

- Registration returns the API key only once and starts the account with `2 free credits`.
- Payments are submitted with a Solana transaction signature through `POST /api/v1/buy-credits`.
- Reserved inbox prefixes include `support`, `sales`, `admin`, and other protected names.

## License

MIT