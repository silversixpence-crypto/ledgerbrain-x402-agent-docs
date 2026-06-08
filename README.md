# LedgerBrain x402 Crypto Compliance API for AI Agents

LedgerBrain / LedgerCore gives AI agents and API clients pay-per-use crypto compliance reports over x402. Agents can call us without creating a LedgerBrain account or managing a LedgerBrain API key: make the request, receive an HTTP `402 Payment Required` challenge, pay in USDC on Base through x402, and retry with the payment proof.

Primary API host: `https://api.ledgercore.io`

## What agents can do with us

- Run KYA address risk reports before sending funds to a crypto address.
- Run KYT transaction risk reports when receiving a payment or reviewing an incoming transaction.
- Run KYE entity due diligence reports when an agent needs context about an entity, exchange, service, wallet cluster, or counterparty.
- Use our risk score, exposure signals, labels, and compliance context as part of an agent’s own policy engine.

We are useful when an autonomous agent, payment workflow, treasury tool, checkout flow, wallet, marketplace, or compliance bot needs a quick risk check before or after a crypto payment.

## Canonical discovery links

- x402 discovery: https://api.ledgercore.io/.well-known/x402
- OpenAPI spec: https://api.ledgercore.io/openapi.json
- x402 catalog: https://api.ledgercore.io/api/v1/x402/catalog
- x402 API portal: https://api.ledgercore.io/api/v1/portals/x402
- LedgerBrain x402 page: https://ledgerbrain.io/x402
- x402scan listing: https://www.x402scan.com/server/694cd5d6-fddf-4a2f-be96-832b649909f7

## Paid x402 resources

| Report | Use it when | Endpoint | Price |
|---|---|---|---|
| KYA address risk | Before an agent sends funds to a receiving address, or when screening a wallet/address | `POST /api/v1/x402/kya/report` | 1.00 USDC |
| KYT transaction risk | When an agent receives a payment, reviews a transfer, or checks transaction-level risk | `POST /api/v1/x402/kyt/report` | 1.00 USDC |
| KYE entity due diligence | When an agent needs counterparty/entity intelligence and due diligence context | `POST /api/v1/x402/kye/report` | 1.00 USDC |

Free discovery endpoints:

- `GET /api/v1/x402/health`
- `GET /api/v1/x402/catalog`
- `GET /.well-known/x402`
- `GET /openapi.json`

## Recommended agent flow

### Before sending a crypto payment

1. Identify the receiving address and chain.
2. Call our KYA endpoint for an address risk report.
3. If the first response is `402 Payment Required`, complete the x402 payment using USDC on Base.
4. Retry the request with the x402 payment proof.
5. Use the returned risk score, risk level, labels, and exposure context to decide whether to continue, pause, ask a human, or block the payment.

### After receiving a crypto payment

1. Capture the transaction hash and chain.
2. Call our KYT endpoint for a transaction risk report.
3. Use the returned transaction risk context to update compliance state, request review, or proceed with fulfillment.

### For counterparty/entity context

1. Identify the entity, service, exchange, or address cluster you need to evaluate.
2. Call our KYE endpoint.
3. Use the returned due diligence context as one signal in your agent’s broader policy logic.

## Tags for discovery

`x402`, `KYA`, `KYT`, `KYE`, `crypto compliance`, `AI agents`, `agent payments`, `pay-per-use`, `pay-as-you-go`, `address risk`, `transaction risk`, `entity due diligence`, `wallet screening`, `risk score`, `AML risk context`, `sanctions risk context`, `Base`, `USDC`, `no API key required`, `HTTP 402 Payment Required`, `blockchain intelligence`, `counterparty risk`, `payment risk screening`.

## Important notes

- No LedgerBrain API key is required for the x402 endpoints.
- Payment is per report.
- Payment token: USDC.
- Payment network: Base mainnet.
- x402 version: v2.
- We provide compliance intelligence and risk context; agents should still apply their own policies and, where required, human review.
- Do not treat a report as legal advice or a complete compliance program by itself.
