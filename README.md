# LedgerCore / LedgerBrain — x402 pay-per-use KYA, KYT, and KYE compliance API for AI agents

Run crypto address risk reports, transaction risk screening, entity due diligence, compliance risk
screening, and auditable compliance reports via x402 payments. No LedgerBrain API key required
for x402 endpoints.

**Primary API host:** `https://api.ledgercore.io`

---

## Overview

LedgerBrain / LedgerCore provides pay-per-use crypto compliance reports over x402. AI agents,
payment bots, treasury tools, wallets, marketplaces, and automation systems can:

- Screen a receiving address before sending funds (KYA).
- Review an incoming transaction after receiving a payment (KYT).
- Look up entity due diligence context for a counterparty or exchange (KYE).

Payment is USDC on Base mainnet, handled by x402 v2. No LedgerBrain API key is required for
x402 endpoints — the x402 payment is the only gate.

---

## What agents can buy

| Report | Use it when | Price |
|--------|-------------|-------|
| **KYA** — address risk report | Before sending funds to a receiving address, or screening a wallet | 1.00 USDC |
| **KYT** — transaction risk report | After receiving a payment, or reviewing an incoming transaction | 1.00 USDC |
| **KYE** — entity intelligence report | Counterparty or entity due diligence context | 1.00 USDC |

Pricing confirmed from live catalog: `https://api.ledgercore.io/api/v1/x402/catalog`

---

## Live endpoints

### Discovery endpoints (free, no payment needed)

| Method | URL | Purpose |
|--------|-----|---------|
| GET | `https://api.ledgercore.io/api/v1/x402/health` | Service health check |
| GET | `https://api.ledgercore.io/api/v1/x402/catalog` | x402 catalog — payable resources and schemas |
| GET | `https://api.ledgercore.io/.well-known/x402` | x402 discovery document |
| GET | `https://api.ledgercore.io/openapi.json` | OpenAPI spec |
| GET | `https://api.ledgercore.io/api/v1/portals/x402` | Human-readable API portal |

### Paid report endpoints

| Method | URL | Report |
|--------|-----|--------|
| POST | `https://api.ledgercore.io/api/v1/x402/kya/report` | KYA address risk report |
| POST | `https://api.ledgercore.io/api/v1/x402/kyt/report` | KYT transaction risk report |
| POST | `https://api.ledgercore.io/api/v1/x402/kye/report` | KYE entity intelligence report |

---

## x402 payment flow

1. Send an unpaid POST request to the chosen paid endpoint.
2. Receive `HTTP 402 Payment Required` with a `PAYMENT-REQUIRED` header.
3. The `PAYMENT-REQUIRED` header contains `payTo`, `price`, `network`, and `token`.
4. Pay in USDC on Base mainnet using your x402-capable client or wallet.
5. Retry the request with the `PAYMENT-SIGNATURE` header containing the payment proof.
6. Receive the compliance report in the response body.

**x402 version:** v2  
**Payment token:** USDC  
**Network:** Base mainnet  
**No LedgerBrain API key required for x402 endpoints.**

---

## Request schemas

### KYA — address risk

```json
{
  "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7Divfna",
  "chain": "bitcoin"
}
```

Required fields: `address`, `chain`.  
Supported chains: `bitcoin`, `ethereum`, `solana`, `binance-smart-chain`, `tron`, `ripple`.  
Optional: `asset` (ERC-20 contract address, ethereum only), `reference_id`.

### KYT — transaction risk

```json
{
  "txid": "4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b",
  "chain": "bitcoin"
}
```

Required fields: `txid`, `chain`.  
Note: the field is `txid`, not `tx_hash`.  
Optional: `asset` (ERC-20 contract address, ethereum only), `timeout`, `reference_id`.

### KYE — entity intelligence

```json
{
  "name": "Binance"
}
```

Required fields: `name` (entity name, minimum 2 characters, e.g. `"Binance"`, `"Luno"`).  
The top matching entity is returned.

---

## Discovery URLs

| Resource | URL |
|----------|-----|
| x402 discovery | `https://api.ledgercore.io/.well-known/x402` |
| x402 catalog | `https://api.ledgercore.io/api/v1/x402/catalog` |
| OpenAPI spec | `https://api.ledgercore.io/openapi.json` |
| API portal | `https://api.ledgercore.io/api/v1/portals/x402` |
| LedgerBrain x402 page | `https://ledgerbrain.io/x402` |
| x402scan listing | `https://www.x402scan.com/server/694cd5d6-fddf-4a2f-be96-832b649909f7` |
| LedgerBrain website | `https://ledgerbrain.io` |
| llms.txt | `https://api.ledgercore.io/llms.txt` |

---

## Example agent prompts

These are example prompts an agent might use to invoke LedgerBrain via x402.

> "Find and buy a LedgerBrain KYA address risk report using x402 for address
> `1A1zP1eP5QGefi2DMPTfTL5SLmv7Divfna` on bitcoin. Max spend: 1 USDC."

> "Use LedgerBrain's x402 KYT API to screen a bitcoin transaction for compliance
> risk. Max spend: 1 USDC."

> "Discover LedgerBrain's x402 catalog, choose the KYE entity intelligence report,
> pay with x402, and return the compliance report."

---

## Examples

- [curl examples](examples/curl.md)
- [JavaScript fetch pattern](examples/javascript-fetch.md)

---

## Documentation

- [Agent integration guide](docs/agent-integration-guide.md)
- [Endpoints reference](docs/endpoints.md)
- [Discovery metadata](docs/discovery-metadata.md)

---

## Discovery tags

`KYA`, `KYT`, `KYE`, `crypto compliance`, `address risk`, `transaction risk`,
`entity due diligence`, `compliance reports`, `compliance risk screening`,
`AML/sanctions risk context`, `AI agents`, `x402`, `pay-per-use`, `pay-as-you-go`,
`USDC`, `Base`, `no LedgerBrain API key required for x402 endpoints`,
`HTTP 402 Payment Required`, `blockchain intelligence`, `counterparty risk`,
`payment risk screening`.

---

## Safety note

LedgerBrain reports provide compliance intelligence and risk context. They are not legal advice,
financial advice, or a replacement for regulated compliance judgment. Always apply your own
policies and, where required, human review. Compliance risk screening includes AML/sanctions-related
risk context where available in the report.
