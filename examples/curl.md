# curl examples

These examples show endpoint shape and expected behavior only.

An unauthenticated request to any paid endpoint returns `HTTP 402 Payment Required`. A real
x402-capable client must then parse the `PAYMENT-REQUIRED` header, complete the USDC payment
on Base mainnet, and retry with the `PAYMENT-SIGNATURE` header. Normal curl cannot complete
the x402 payment step automatically.

Do not include fake signed payment payloads in scripts. Use a real x402-capable client for
end-to-end payment.

---

## Discover payable resources (free, no payment)

```bash
# x402 discovery document
curl -sS https://api.ledgercore.io/.well-known/x402 | jq

# Full catalog with resource schemas and pricing
curl -sS https://api.ledgercore.io/api/v1/x402/catalog | jq

# OpenAPI spec info
curl -sS https://api.ledgercore.io/openapi.json | jq '.info'

# Health check
curl -sS https://api.ledgercore.io/api/v1/x402/health | jq
```

---

## KYA — address risk report

Use before sending funds to a receiving address.

```bash
curl -i -X POST https://api.ledgercore.io/api/v1/x402/kya/report \
  -H 'Content-Type: application/json' \
  -d '{
    "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7Divfna",
    "chain": "bitcoin"
  }'
```

Expected first response (no payment header sent):

```text
HTTP/2 402 Payment Required
PAYMENT-REQUIRED: <challenge with payTo, price, network, token>
```

After completing x402 payment with an x402-capable client, retry with:

```bash
curl -i -X POST https://api.ledgercore.io/api/v1/x402/kya/report \
  -H 'Content-Type: application/json' \
  -H 'PAYMENT-SIGNATURE: <your-signed-payment-proof>' \
  -d '{
    "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7Divfna",
    "chain": "bitcoin"
  }'
```

---

## KYT — transaction risk report

Use after receiving a payment or when reviewing a transaction.

Note: the request field is `txid`, not `tx_hash`.

```bash
curl -i -X POST https://api.ledgercore.io/api/v1/x402/kyt/report \
  -H 'Content-Type: application/json' \
  -d '{
    "txid": "4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b",
    "chain": "bitcoin"
  }'
```

Expected first response (no payment header sent):

```text
HTTP/2 402 Payment Required
PAYMENT-REQUIRED: <challenge with payTo, price, network, token>
```

After completing x402 payment, retry with the `PAYMENT-SIGNATURE` header.

---

## KYE — entity intelligence report

Use for counterparty or entity due diligence context.

Note: the request field is `name`, not `entity`.

```bash
curl -i -X POST https://api.ledgercore.io/api/v1/x402/kye/report \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Binance"
  }'
```

Expected first response (no payment header sent):

```text
HTTP/2 402 Payment Required
PAYMENT-REQUIRED: <challenge with payTo, price, network, token>
```

After completing x402 payment, retry with the `PAYMENT-SIGNATURE` header.

---

## Notes

- All paid endpoints return `402 Payment Required` on the first unauthenticated request.
  This is expected behavior, not an error.
- The `PAYMENT-REQUIRED` header contains the payment destination, price, network, and token.
- Payment is 1.00 USDC on Base mainnet.
- No LedgerBrain API key is required for x402 endpoints.
- Use `txid` for KYT (not `tx_hash`).
- Use `name` for KYE (not `entity`).
- Supported chains: `bitcoin`, `ethereum`, `solana`, `binance-smart-chain`, `tron`, `ripple`.
