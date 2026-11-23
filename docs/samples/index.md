---
sidebar_position: 1
---

# Samples

Two flows carry the whole wallet: the issuance round and the presentation
round. Both are exercised end to end by the vertical's Playwright harness
against the real browser UI.

## The issuance round

1. The issuer renders a Credential Offer as a QR code or deep link.
2. The wallet scans it and starts the intake.
3. The wallet completes the OID4VCI authorization round.
4. The credential lands in the vault, sealed.

```text
wallet://offer?credential_offer_uri=https%3A%2F%2Fissuer.example%2Foffers%2Fabc
```

## The presentation round

1. The verifier renders an Authorization Request as a QR code or deep link.
2. The wallet scans it and resolves the request into the exact claim set.
3. The consent screen shows that claim set.
4. The user approves; the wallet delivers the presentation.

```text
openid4vp://?client_id=x509_hash%3A...&request_uri=https%3A%2F%2Fverifier.example%2Freq%2F1
```

## What the samples demonstrate

| Sample | Demonstrates |
|---|---|
| Pre-authorized issuance | The `tx_code` flow, the nonce endpoint, the proof |
| Authorization-code issuance | PAR + PKCE, the authorize round, the token exchange |
| Deferred issuance | The deferred poll and the notification |
| Presentation with DCQL | Claim-set selection and selective disclosure |
| Encrypted response | The `direct_post.jwt` form and response decryption |
| DC API | The browser-mediated channel and the origin-bounded promise |

## Running them

The e2e harness is the source of truth:

```bash
git clone https://github.com/Wallet8-dev/wallet8-e2e.git
cd wallet8-e2e
npm install
npx playwright test
```

Every setup step goes through the UI or the BFF proxy - the harness never
touches a database directly, so a passing run is a statement about the
product a user sees.
