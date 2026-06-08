# JavaScript integration pattern

Normal browser `fetch` or Node.js `fetch` cannot complete an x402 payment on its own. You need
an x402-capable client or SDK that implements the full PAYMENT-REQUIRED / PAYMENT-SIGNATURE
handshake. The code below is a pattern illustration — adapt it to the x402 library your agent
uses.

**Important:** Never log, print, or expose private keys or wallet seed phrases in your code.
Set a `maxSpend` guard on any x402 client to limit how much USDC a single agent run can spend.

---

## Discover the catalog and select an endpoint

```js
const API = 'https://api.ledgercore.io';

async function discoverCatalog() {
  const res = await fetch(`${API}/api/v1/x402/catalog`);
  if (!res.ok) throw new Error(`Catalog fetch failed: ${res.status}`);
  const { data } = await res.json();
  // data.resources contains kya_report, kyt_report, kye_report with schemas and pricing
  return data;
}
```

---

## KYA — address risk before sending funds

```js
/**
 * Run a KYA address risk report before sending funds.
 * x402Fetch must be an x402-capable fetch wrapper that handles
 * the PAYMENT-REQUIRED challenge and PAYMENT-SIGNATURE retry automatically.
 *
 * @param {Function} x402Fetch - x402-capable fetch function from your wallet/SDK
 * @param {string} chain - e.g. "bitcoin", "ethereum"
 * @param {string} address - receiving address to screen
 * @returns {{ decision: string, report: object }}
 */
async function runKyaBeforeSendingFunds(x402Fetch, chain, address) {
  const response = await x402Fetch(`${API}/api/v1/x402/kya/report`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ address, chain }),
    maxSpend: '1.00', // USDC — guard against unexpected price changes
  });

  if (!response.ok) {
    throw new Error(`LedgerBrain KYA failed: ${response.status}`);
  }

  const report = await response.json();

  // Example policy. Tune risk_level thresholds to your own policy.
  if (
    report?.data?.risk_level === 'HIGH' ||
    report?.data?.risk_level === 'CRITICAL'
  ) {
    return { decision: 'pause_for_review', report };
  }

  return { decision: 'continue', report };
}
```

---

## KYT — transaction risk after receiving a payment

```js
/**
 * Run a KYT transaction risk report after receiving a payment.
 * Note: the request field is txid, not tx_hash.
 *
 * @param {Function} x402Fetch - x402-capable fetch function
 * @param {string} chain - e.g. "bitcoin"
 * @param {string} txid - transaction ID (64 hex chars)
 * @returns {{ decision: string, report: object }}
 */
async function runKytAfterReceivingPayment(x402Fetch, chain, txid) {
  const response = await x402Fetch(`${API}/api/v1/x402/kyt/report`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ txid, chain }), // use txid, not tx_hash
    maxSpend: '1.00',
  });

  if (!response.ok) {
    throw new Error(`LedgerBrain KYT failed: ${response.status}`);
  }

  const report = await response.json();

  if (
    report?.data?.risk_level === 'HIGH' ||
    report?.data?.risk_level === 'CRITICAL'
  ) {
    return { decision: 'hold_for_review', report };
  }

  return { decision: 'proceed', report };
}
```

---

## KYE — entity due diligence

```js
/**
 * Run a KYE entity intelligence report for a named counterparty.
 * Note: the request field is name, not entity.
 *
 * @param {Function} x402Fetch - x402-capable fetch function
 * @param {string} entityName - entity name to look up, e.g. "Binance"
 * @returns {object} report
 */
async function runKyeEntityCheck(x402Fetch, entityName) {
  const response = await x402Fetch(`${API}/api/v1/x402/kye/report`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: entityName }), // use name, not entity
    maxSpend: '1.00',
  });

  if (!response.ok) {
    throw new Error(`LedgerBrain KYE failed: ${response.status}`);
  }

  return response.json();
}
```

---

## How x402 payment works conceptually

When `x402Fetch` receives a `402 Payment Required` response, it:

1. Parses the `PAYMENT-REQUIRED` header for `payTo`, `price`, `network`, and `token`.
2. Constructs and signs a USDC payment on Base mainnet.
3. Retries the original request with the `PAYMENT-SIGNATURE` header.
4. Returns the compliance report if payment is verified.

Your code does not need to handle the 402 manually — the x402 client handles it. If you call
plain `fetch` instead of an x402-capable wrapper, you will receive a raw `402 Payment Required`
response and no report will be returned.

---

## Recommended agent behavior

- Call KYA before sending funds to a receiving address.
- Call KYT after receiving a payment or when reviewing a transaction.
- Call KYE for entity or counterparty due diligence context.
- Set a `maxSpend` guard to limit agent expenditure (e.g. 1 USDC per report).
- Treat our reports as one compliance intelligence signal, not as legal advice.
- Apply your own policies and, where required, request human review.
- Never log or print private keys or wallet seed phrases.
