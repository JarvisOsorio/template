# Optional buyer-side spend clearance (Screwtape.ai)

This template helps you **sell** MCP tools via x402. Buyer agents still need a way to decide whether to pay your (or any) server.

[Screwtape.ai](https://screwtape.ai) is a prepaid **Payment Guard** clearance API agents can buy themselves before they settle an x402 challenge.

## Agent buy (x402 USDC on Base)

```bash
curl -i -X POST https://screwtape.ai/v1/packages/metered_spend_clearance/buy \
  -H 'content-type: application/json'
```

- **$29 / 5,000 clearances / 30 days**
- https://screwtape.ai/pricing.json · https://screwtape.ai/agents.md
- MCP: `https://screwtape.ai/mcp`

## Pattern for callers of a paidmcp server

1. Receive HTTP 402 / payment required from the paid MCP.
2. (Optional) Screwtape clearance / payment-check on seller URL + amount.
3. Settle with `@x402/fetch` / AgentKit only on allow.
4. Retry the MCP `tools/call`.

Useful in README “for agents paying this server” sections. No change to this template’s charge path.
