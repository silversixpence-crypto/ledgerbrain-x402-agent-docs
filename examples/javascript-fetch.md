# JavaScript integration pattern

Use an x402-capable fetch/client. The exact client depends on the wallet and x402 library your agent uses.

Pseudo-code:

```js
const API = 'https://api.ledgercore.io';

async function runKyaBeforeSendingFunds(x402Fetch, chain, address) {
  const response = await x402Fetch(`${API}/api/v1/x402/kya/report`, {
    method: 'POST',
    headers: { 'content-type': 'application/json' },
    body: JSON.stringify({ chain, address })
  });

  if (!response.ok) {
    throw new Error(`LedgerBrain KYA failed: ${response.status}`);
  }

  const report = await response.json();

  // Agent policy example. Tune thresholds to your own policy.
  if (report?.data?.risk_level === 'HIGH' || report?.data?.risk_level === 'CRITICAL') {
    return { decision: 'pause_for_review', report };
  }

  return { decision: 'continue', report };
}
```

Recommended agent behavior:

- Call KYA before sending funds to a receiving address.
- Call KYT after receiving a payment or when reviewing a transaction hash.
- Call KYE for entity/counterparty due diligence.
- Treat our report as one compliance intelligence signal, not as legal advice.
```
