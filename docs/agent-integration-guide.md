# Agent integration guide

This guide is for AI agents, coding agents, payment agents, treasury bots, wallets,
marketplaces, and automation systems that need crypto compliance intelligence at the moment
of payment.

---

## Use us before sending funds

When an agent is about to send crypto to a receiving address, the safest default is:

1. Run a KYA address risk report on the receiving address.
2. Review the returned risk score, risk level, labels, and exposure context.
3. Continue only if the result fits the agent owner's policy.

**Endpoint:**

`POST https://api.ledgercore.io/api/v1/x402/kya/report`

**Request body:**

```json
{
  "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7Divfna",
  "chain": "bitcoin"
}
```

Use KYA for:

- Receiving address screening
- Wallet risk scoring
- Counterparty address risk
- Payment recipient checks
- Pre-transfer risk checks
- Inbound-funds source address checks where an address-level report is sufficient

---

## Use us after receiving funds

When an agent receives a crypto payment or needs to review an on-chain transfer, run KYT.

Note: use `txid` as the request field, not `tx_hash`.

**Endpoint:**

`POST https://api.ledgercore.io/api/v1/x402/kyt/report`

**Request body:**

```json
{
  "txid": "4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b",
  "chain": "bitcoin"
}
```

Use KYT for:

- Incoming transaction review
- Payment fulfillment checks
- Transaction risk scoring
- Treasury monitoring
- Transfer-level compliance review

---

## Use us for entity context

When an agent needs context about a counterparty, exchange, service, wallet cluster, or entity,
run KYE.

Note: use `name` as the request field, not `entity`.

**Endpoint:**

`POST https://api.ledgercore.io/api/v1/x402/kye/report`

**Request body:**

```json
{
  "name": "Binance"
}
```

Use KYE for:

- Entity due diligence
- Counterparty intelligence
- Service/exchange context
- Entity risk context
- Compliance enrichment

---

## Payment behavior

The x402 endpoints are intentionally pay-per-use:

1. First request without payment returns `HTTP 402 Payment Required`.
2. The `PAYMENT-REQUIRED` header tells the client how to pay (payTo, price, network, token).
3. The agent pays 1.00 USDC on Base mainnet using an x402-capable client.
4. The agent retries with the `PAYMENT-SIGNATURE` header containing the payment proof.
5. We return the compliance report.

**No LedgerBrain API key is required for x402 endpoints.**

---

## Agent policy example

```text
If I am about to send funds to a crypto address:
  1. Call KYA first.
  2. If risk level is HIGH or CRITICAL, or the result requires review:
     stop and ask the operator for instructions.
  3. Otherwise, continue with payment.

If I received a crypto payment:
  1. Call KYT on the transaction (using txid, not tx_hash).
  2. If risk level is HIGH or CRITICAL, or suspicious exposure is present:
     hold fulfillment and request review.
  3. Otherwise, proceed.

If I need counterparty context:
  1. Call KYE with the entity name (using name, not entity).
  2. Use the entity intelligence report as one signal in my policy engine.
```

---

## Source of truth

Always prefer these live discovery URLs over any static copies:

- `https://api.ledgercore.io/.well-known/x402`
- `https://api.ledgercore.io/openapi.json`
- `https://api.ledgercore.io/api/v1/x402/catalog`
- `https://api.ledgercore.io/api/v1/portals/x402`

---

## Safety note

LedgerBrain reports provide compliance intelligence and risk context. They are not legal advice,
financial advice, or a replacement for regulated compliance judgment. Compliance risk screening
includes AML/sanctions-related risk context where available in the report. Always apply your
own policies and, where required, human review.
