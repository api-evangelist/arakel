# ARAKEL Machine Evidence Network

Official-source machine evidence for autonomous agents.

Base URL: https://proof.arakelproof.space
OpenAPI: https://proof.arakelproof.space/openapi.json
MCP: https://proof.arakelproof.space/mcp
AgentCard: https://proof.arakelproof.space/.well-known/agent-card.json
x402 manifest: https://proof.arakelproof.space/.well-known/x402
Pricing: https://proof.arakelproof.space/pricing
Terms: https://proof.arakelproof.space/terms

## Buyer flow
1. Run a free preflight.
2. If payment_recommended=false, do not pay.
3. If true, call the returned paid route and inspect HTTP 402.
4. Authorize USDC only if price and request are acceptable.
5. Verify the signed machine-readable result.

Low-cost lookups: OFAC, FDA, USAspending and Federal Register are $0.005. Counterparty government check is $0.03. The live HTTP 402 challenge is authoritative.