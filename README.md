# CryptoAgentMail Skills

Shareable skill package for the live CryptoAgentMail service.

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

- `1 credit = 1 USDC`
- `create inbox = 1 credit`
- `send email = 0.0012 credits`
- `receive email = free`
- `memory = 0.05 credits/day`

## Notes

- Registration returns the API key only once.
- Payments are submitted with a Solana transaction signature through `POST /api/v1/buy-credits`.

## License

MIT
