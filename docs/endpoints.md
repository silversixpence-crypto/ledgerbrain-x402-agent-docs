# LedgerBrain x402 endpoints reference

Base URL: `https://api.ledgercore.io`

Always check the live catalog for current schemas and pricing:
`https://api.ledgercore.io/api/v1/x402/catalog`

---

## Discovery endpoints (free, no payment needed)

| Method | URL | Purpose |
|--------|-----|---------|
| GET | `https://api.ledgercore.io/api/v1/x402/health` | Service health check |
| GET | `https://api.ledgercore.io/api/v1/x402/catalog` | x402 catalog — payable resources, schemas, and pricing |
| GET | `https://api.ledgercore.io/.well-known/x402` | x402 discovery document for agents and indexers |
| GET | `https://api.ledgercore.io/openapi.json` | OpenAPI spec |
| GET | `https://api.ledgercore.io/api/v1/portals/x402` | Human-readable x402 API portal |

---

## Paid report endpoints

### KYA — address risk report

`POST https://api.ledgercore.io/api/v1/x402/kya/report`

Use this before an agent sends funds to a receiving crypto address, or when screening an address
for compliance risk.

**Request schema:**

```json
{
  "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7Divfna",
  "chain": "bitcoin"
}
```

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `address` | Yes | string | Blockchain address to analyze |
| `chain` | Yes | string | Blockchain network (see supported chains below) |
| `asset` | No | string | ERC-20 contract address (ethereum only) |
| `reference_id` | No | string | Optional client reference ID |

**Price:** 1.00 USDC per report  
**Keywords:** KYA, address risk, wallet screening, receiving address, payment recipient,
crypto compliance, AI agents, x402, USDC, Base.

---

### KYT — transaction risk report

`POST https://api.ledgercore.io/api/v1/x402/kyt/report`

Use this when an agent receives a crypto payment, reviews a transaction, or needs
transaction-level compliance risk context.

Note: the request field is `txid`, not `tx_hash`.

**Request schema:**

```json
{
  "txid": "4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b",
  "chain": "bitcoin"
}
```

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `txid` | Yes | string | Transaction ID (64 hex chars; 0x prefix optional for ETH) |
| `chain` | Yes | string | Blockchain network (see supported chains below) |
| `asset` | No | string | ERC-20 contract address (ethereum only) |
| `timeout` | No | integer | Request timeout in ms (default 300000, min 1000, max 900000) |
| `reference_id` | No | string | Optional client reference ID |

**Price:** 1.00 USDC per report  
**Keywords:** KYT, transaction risk, incoming payment, transaction ID, payment review,
crypto compliance, AI agents, x402, USDC, Base.

---

### KYE — entity intelligence report

`POST https://api.ledgercore.io/api/v1/x402/kye/report`

Use this when an agent needs entity due diligence or counterparty intelligence.

Note: the request field is `name`, not `entity`.

**Request schema:**

```json
{
  "name": "Binance"
}
```

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `name` | Yes | string | Entity name (min 2 chars, e.g. `"Binance"`, `"Luno"`) |

The top matching entity is returned.

**Price:** 1.00 USDC per report  
**Keywords:** KYE, entity due diligence, counterparty risk, entity profile,
crypto compliance, AI agents, x402, USDC, Base.

---

## Supported chains

`bitcoin`, `ethereum`, `solana`, `binance-smart-chain`, `tron`, `ripple`

---

## Pricing summary

| Report | Price |
|--------|-------|
| KYA — address risk | 1.00 USDC |
| KYT — transaction risk | 1.00 USDC |
| KYE — entity intelligence | 1.00 USDC |

Pricing confirmed from live catalog. Always use the live catalog as the source of truth.

---

## x402 payment

- Protocol: x402 v2
- Payment token: USDC
- Network: Base mainnet
- No LedgerBrain API key required for x402 endpoints
- Payment challenge header: `PAYMENT-REQUIRED`
- Payment proof header: `PAYMENT-SIGNATURE`
