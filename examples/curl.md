# curl examples

These examples show endpoint shape only. A normal unauthenticated request to a paid endpoint should return HTTP `402 Payment Required`. An x402-capable client must then pay and retry with the payment proof.

## Discover payable resources

```bash
curl -sS https://api.ledgercore.io/.well-known/x402 | jq
curl -sS https://api.ledgercore.io/api/v1/x402/catalog | jq
curl -sS https://api.ledgercore.io/openapi.json | jq '.info'
```

## KYA before sending funds to a receiving address

```bash
curl -i -X POST https://api.ledgercore.io/api/v1/x402/kya/report \
  -H 'content-type: application/json' \
  -d '{
    "chain": "bitcoin",
    "address": "bc1q4csuanvy2ch5f4fr2pv7ju5f54cet0xpp38umkyrxd8t5axcek8q3g7f7u"
  }'
```

Expected first response without payment:

```text
HTTP/1.1 402 Payment Required
```

Then retry with the x402 payment proof using an x402-capable client.

## KYT after receiving a payment

```bash
curl -i -X POST https://api.ledgercore.io/api/v1/x402/kyt/report \
  -H 'content-type: application/json' \
  -d '{
    "chain": "bitcoin",
    "tx_hash": "TRANSACTION_HASH_HERE"
  }'
```

## KYE for entity due diligence

```bash
curl -i -X POST https://api.ledgercore.io/api/v1/x402/kye/report \
  -H 'content-type: application/json' \
  -d '{
    "entity": "ENTITY_OR_COUNTERPARTY_HERE"
  }'
```
