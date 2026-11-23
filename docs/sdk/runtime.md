---
sidebar_position: 1
---

# wallet8 Rust SDK - the wallet client libraries

The `wallet8` facade: the OID4VCI issuance client, the OID4VP presentation
client, and the holder-service contracts. Consumed by any vertical's
backend that needs to act as, drive, or verify a wallet.

## What it provides

### Issuance (OID4VCI 1.0)

- Credential Offer intake by value and by reference.
- Issuer metadata identity check and authorization-server selection.
- Both grant flows: authorization code with PAR + PKCE, and
  pre-authorized with `tx_code`.
- The Nonce Endpoint and `openid4vci-proof+jwt` proofs (with key
  attestations).
- Credential, Deferred, and Notification endpoints.
- Batch issuance.

### Presentation (OID4VP 1.0)

- JAR request-object signature verification (ES256).
- The Client Identifier Prefix resolver: `x509_san_dns`, `x509_hash`,
  `verifier_attestation`, `redirect_uri`, `openid_federation`, DIDs.
- DCQL with the exact claim-set and credential-set selection rules.
- `vp_token` assembly for SD-JWT VC and mdoc.
- `direct_post` and the encrypted response form (ECDH-ES over
  A128/A256GCM).
- The Digital Credentials API channel (`dc_api` / `dc_api.jwt`).
- The error vocabulary and the `transaction_data` rejection gate.

### Device binding and revocation

- DPoP proofs (RFC 9449) for sender-constrained access tokens.
- The Token Status List decode, list parse, bit-array read, TTL-cached
  fetch, and the vault's sync walk.

## Using it

The facade is the only supported import surface:

```rust
use wallet8::wallet::issuance::IssuanceClient;
use wallet8::wallet::presentation::PresentationClient;
```

Each protocol function cites the specification section it implements, so
an auditor can walk the code against the normative text.

## Design

- The SDK never stores credentials; the holder owns persistence.
- The SDK never holds a long-lived secret; keys are per wallet and
  registered once.
- Every outbound token is sender-constrained where the standard allows it.

## Full documentation

- [Rust SDK reference](/docs/reference/sdk/) - the harvested repository docs.
