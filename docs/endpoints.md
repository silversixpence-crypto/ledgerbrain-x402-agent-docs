# LedgerBrain x402 endpoints

Base URL: `https://api.ledgercore.io`

## Discovery endpoints

| Method | URL | Purpose |
|---|---|---|
| GET | `https://api.ledgercore.io/.well-known/x402` | x402 discovery for agents and indexers |
| GET | `https://api.ledgercore.io/openapi.json` | OpenAPI spec |
| GET | `https://api.ledgercore.io/api/v1/x402/catalog` | x402 catalog with payable resources and pricing |
| GET | `https://api.ledgercore.io/api/v1/portals/x402` | Human-readable x402 API portal |
| GET | `https://api.ledgercore.io/api/v1/x402/health` | Service health check |

## Paid report endpoints

### KYA address risk report

`POST https://api.ledgercore.io/api/v1/x402/kya/report`

Use this before an agent sends funds to a receiving crypto address, or when an agent needs address-level risk screening.

Keywords: KYA, address risk, wallet screening, receiving address, payment recipient, crypto compliance, AI agents, x402, USDC, Base.

### KYT transaction risk report

`POST https://api.ledgercore.io/api/v1/x402/kyt/report`

Use this when an agent receives a crypto payment, reviews a transaction hash, or needs transaction-level compliance risk context.

Keywords: KYT, transaction risk, incoming payment, transaction hash, payment review, crypto compliance, AI agents, x402, USDC, Base.

### KYE entity due diligence report

`POST https://api.ledgercore.io/api/v1/x402/kye/report`

Use this when an agent needs entity due diligence or counterparty intelligence.

Keywords: KYE, entity due diligence, counterparty risk, entity profile, crypto compliance, AI agents, x402, USDC, Base.

## Pricing

- KYA: 1.00 USDC per report
- KYT: 1.00 USDC per report
- KYE: 1.00 USDC per report

## x402 payment

- Protocol: x402 v2
- Payment token: USDC
- Network: Base mainnet
- LedgerBrain API key: not required for x402 endpoints
