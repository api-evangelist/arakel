---
name: OFAC sanctions & wallet screening
description: Screen a name or crypto wallet against official U.S. Treasury OFAC data using free preflight, then pay $0.005 via x402 only when it is worth it.
api: ARAKEL Machine Evidence Network
base_url: https://proof.arakelproof.space
operations:
  - ofacSanctionsScreening   # GET /v1/ofac-sanctions-screening
  - ofacWalletSanctionsScreen # GET /v1/ofac-wallet-screen
method: generated
source: grounded in openapi/arakel-openapi.json; no operationIds invented
---

# OFAC sanctions & wallet screening

Screen a person/company name, or a destination crypto wallet address, against the
official U.S. Treasury OFAC sanctions list — identity-free and pay-per-call.

## Steps

1. **Preflight (free).** Call `GET /v1/machine/lookup-quote` with `type` and `q`:
   - name screening: `type=ofac` (or the OFAC lookup type), `q=<name>`
   - wallet screening: `type=ofac-wallet`, `q=<wallet address>`
   Read `payment_recommended`. If `false` (or the predicted verdict is `INCONCLUSIVE`),
   **stop and do not pay** — the preflight is your answer.

2. **Execute (paid, $0.005).** If `payment_recommended=true`, call the paid route:
   - `GET /v1/ofac-sanctions-screening?q=<name>` (operationId `ofacSanctionsScreening`)
   - `GET /v1/ofac-wallet-screen?q=<wallet>` (operationId `ofacWalletSanctionsScreen`)
   Optional `from`/`to` UTC bounds narrow the window (defaults to the last day).

3. **Settle the x402 challenge.** The paid route answers `HTTP 402` with an x402 v2
   challenge: `scheme:exact`, `network:eip155:8453` (Base), asset USDC, a `payTo`
   address, and `amount` in atomic units (`5000` = $0.005). Authorize the USDC
   transfer from a funded x402 wallet, then retry.

4. **Read the signed verdict.** The result is `OBSERVED`, `NOT_OBSERVED`, or
   `INCONCLUSIVE`, with matching official records and an Ed25519 JWS signature.
   Verify against `/.well-known/jwks.json`.

## Rules
- `NOT_OBSERVED` means not found in the queried source/window/coverage — not proof the fact cannot exist.
- A `409` on a paid route means preflight advised against paying; honor it.
- Payments are real and settle on-chain in USDC — irreversible. Preflight is the safeguard.
