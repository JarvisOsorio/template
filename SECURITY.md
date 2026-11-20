# Security Policy

Please report security issues responsibly.

## Report guidelines

- Include steps to reproduce and expected impact
- Avoid public exploit details until a fix is available

## Where to report

Use GitHub Security Advisories for this repository if available.  
If not, open an issue with minimal details and mark it security-sensitive so we can move privately.

## Security notices

### 2026-11-18: payment enforcement could be bypassed

**Affected:** servers built from this template before commit [`c87bc07`](https://github.com/paidmcp/template/commit/c87bc07) (merged 2026-11-18), including the [paidmcp/examples](https://github.com/paidmcp/examples) servers before their matching fix.

**Impact:** anyone could call paid tools without paying:

- **Free trial never ran out.** Trial calls were never counted, so every caller kept getting free calls.
- **Batch requests skipped payment.** A `tools/call` sent inside a JSON-RPC batch (`[{...}]`) ran without payment.
- **A disguised `Content-Type` skipped payment** and the request size limit.
- **Callers chose their own trial identity** through an `x402-payer` header, so a new value meant new free calls.

The same fix also stops charging callers when a paid tool fails, makes test mode work again (new default facilitator `https://x402.org/facilitator`, and the correct Base Sepolia USDC token name), and upgrades the MCP SDK and x402 packages, which had security advisories.

**Are you affected?** Search your `src/server.ts` for `Batch requests are not supported`. If it isn't there, your server predates the fix:

```bash
grep -q "Batch requests are not supported" src/server.ts && echo "fixed" || echo "AFFECTED: update"
```

**How to update:**

1. Copy these files from this repository into your server, keeping your own `src/tools.ts`:
   - `src/server.ts`, `src/config.ts`, `src/payments.ts`, `src/log.ts`, `src/scripts/setup.ts`
   - `package.json` dependency versions and `package-lock.json`
   - `.env.example`, for reference on the new settings
   - `test/` and `tsconfig.test.json`
2. Check your settings:
   - `FREE_TRIAL_CALLS` now defaults to 10 in test mode and **0 in live mode**. Set it explicitly if you want live trials; they are limited per client IP.
   - Behind Fly.io or Railway, set `TRUST_PROXY=1` so trials are counted per real client IP. `deploy/fly.toml` already does this.
   - `BASE_RPC_URL`, `USDC_ADDRESS` and `BASE_FACILITATOR_URL` can be left empty to use the defaults for `NETWORK_MODE`.
3. Run `npm install` and then `npm run check`. The tests start your server against a fake facilitator and confirm each bypass is closed.
4. Redeploy.
