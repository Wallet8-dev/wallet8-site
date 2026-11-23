---
sidebar_position: 2
---

# Security

Wallet8 inherits the Sentin8 platform's security model by construction and
adds the wallet role's own guarantees. Nothing here is configuration - a
deployment cannot opt out of these properties.

## The inherited platform pillars

1. **Post-quantum cryptography, exclusively.** Every TLS connection
   negotiates X25519MLKEM768 with ML-DSA-65 certificate signatures, anchored
   by an SLH-DSA-SHA2-128s root CA. There is no classical fallback.
2. **One identity plane.** Sentin8:Auth is the only token issuer and JWKS
   authority. Wallet8 mints nothing.
3. **Live authorization - Shield.** RBAC, ABAC, and ReBAC are decided per
   request against roles resolved live; a demoted operator loses access on
   the next request.
4. **Mandatory tenant isolation.** Every tenant table is RLS-forced and no
   role carries BYPASSRLS.
5. **Certificate-only workload identity.** A workload's identity is its
   Acme-issued client certificate - no client secrets, no static API keys.

## What Wallet8 adds

### Consent before data

A presentation has two distinct steps: `resolve` returns the exact claim
set the verifier asked for, `deliver` sends it. The app must show the
resolved set and the user must approve before `deliver` is called. There is
no single call that both decides and reveals.

### Sealed credentials at rest

Credentials are stored as AES-256-GCM envelopes in the holder's schema.
The sealing key derives from the service's own secret; the plaintext
credential never exists in an application table. A database copy without
that secret yields nothing readable.

### Device-bound proofs

One device key per wallet, registered by its public half. Issuance proofs
and DPoP tokens are sender-constrained, so a stolen credential is inert
without the private key that never leaves the device.

### Selective disclosure by construction

The presentation is assembled from the DCQL query's selected claim set, not
from the whole credential. Over-disclosure is not a policy the app can
violate - the SDK cannot express it.

### Revocation awareness

The status sync walks the Token Status List. A revoked credential is
refused locally, so the verifier never sees a presentation that was doomed
to fail.

### An audit trail the user owns

Every verification event is recorded: which verifier, which claims, when.
That is the wallet's duty under OID4VP 1.0 §15.1, and it is the user's own
record.

## Reading further

- The platform-wide model: [SECURITY.md](https://github.com/Wallet8-dev/wallet8-site/blob/main/SECURITY.md).
- The wallet design: `wallet8/DESIGN.md` and the holder's own `DESIGN.md`.
- The standards baseline: `wallet8/BUSINESS.md` §3.
