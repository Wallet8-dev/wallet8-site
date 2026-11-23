---
title: "wallet8 Rust SDK - Business rules (BRD)"
sidebar_label: "Business rules (BRD)"
mdx:
  format: md
custom_edit_url: https://github.com/Wallet8-dev/wallet8-sdk/edit/main/BUSINESS.md
---
# wallet8 Rust SDK - Business Requirements & Functionality

> Audience: product owners, business analysts, and integrators evaluating
> what the **wallet8 Rust SDK** does for the business. For architecture read
> `README.md`; for the vertical's standards baseline read the `wallet8`
> repository's `BUSINESS.md`.

## 1. Business Purpose

The wallet8 Rust SDK is what makes a wallet a *product* rather than a
research project. Digital-credential protocols are specified in thousands
of normative pages; implementing them incorrectly does not fail loudly, it
fails at a real verifier with a real user watching.

The SDK exists so that:

- **A vertical ships a wallet in weeks, not years.** The protocol work is
  already done, reviewed against the normative text, and cited section by
  section.
- **Compliance is auditable.** Every protocol function names the
  specification section it implements, so an auditor can diff behavior
  against the standard rather than trusting a claim.
- **Backends can verify what the wallet presents.** The same client
  library that answers a verifier request can be used server-side to
  check one - one implementation, two sides.
- **Behavior is identical across verticals.** A bank, a hotel group, and a
  health provider embedding the SDK behave the same way in front of the
  same verifier.

## 2. Business Scope (What the SDK Provides)

### Issuance - OID4VCI 1.0 Final

| Capability | Why the business cares |
|---|---|
| Credential Offer intake (by value and by reference) | A user can onboard from a QR code, an email, or an app deep link |
| Issuer metadata identity check | A malicious issuer cannot pass itself off as a trusted one |
| Authorization-server selection | Interoperates with issuers that delegate authorization |
| Authorization code with PAR + PKCE | The high-assurance issuance path |
| Pre-authorized with `tx_code` | The low-friction path, still bound to a user secret |
| Nonce endpoint and `openid4vci-proof+jwt` | Replay-resistant proofs |
| Key attestation | Proves the key lives in a trusted keystore |
| Credential, Deferred, Notification endpoints | Supports instant and asynchronous issuance |
| Batch issuance | Multiple credentials in one round trip |

### Presentation - OID4VP 1.0 Final

| Capability | Why the business cares |
|---|---|
| JAR request-object signature verification | A phishing request is rejected before a claim is shown |
| Client Identifier Prefix resolver (`x509_san_dns`, `x509_hash`, `verifier_attestation`, `redirect_uri`, `openid_federation`, DIDs) | Works across every verifier identity model in use |
| DCQL selection (claim sets and credential sets) | The user presents only what was asked for |
| `vp_token` assembly for SD-JWT VC and mdoc | Both major credential formats from one client |
| `direct_post` and encrypted responses | Compatible with verifiers that require confidentiality in transit |
| Digital Credentials API channel | Works inside the browser's native credential mediation |
| Error vocabulary and `transaction_data` rejection | Predictable failure behavior; no silent downgrade |

### Device binding and revocation

| Capability | Why the business cares |
|---|---|
| DPoP proofs (RFC 9449) | A stolen access token is useless without the device key |
| Token Status List decode, parse, and cached fetch | Revocation awareness without a round trip per presentation |

## 3. Business Capabilities

| Capability | Business rule |
|---|---|
| Cite the standard | Every protocol function references the specification section it implements |
| Never invent behavior | Where the standard is silent, the SDK rejects rather than guesses |
| Fail closed | A verification that cannot complete is a rejection, never an assumption of trust |
| No credential storage | The SDK is a client library; persistence belongs to the holder |
| No long-lived secrets | Keys are per-wallet and registered once by public half |
| Sender-constrain everything | Every token the standards allow to be bound is bound |

## 4. Business Events (Outbound)

None. The SDK is a library: it emits no events and owns no state. Events
are produced by the holder service that composes it.

## 5. Business Rules & Invariants

1. **One facade.** Consumers import `wallet8`, never a leaf crate
   directly - the supported integration surface is a product decision.
2. **The normative text wins.** A conformance suite passing does not
   override the specification; the suite verifies, the spec governs.
3. **No silent degradation.** An unsupported request shape is an error,
   not a partial answer.
4. **No plaintext credential handling.** The SDK never writes a credential
   anywhere.
5. **Deterministic selection.** DCQL selection returns the first
   satisfiable option in the verifier's declared order - the
   least-information outcome.
6. **Every proof is fresh.** A nonce rides every proof and key-binding
   JWT.

## 6. Target-User Roles & Use Cases

| Persona | Use case |
|---|---|
| Vertical backend developer | Embeds the SDK to run issuance or verification server-side |
| Wallet app developer | Uses the SDK through the holder for anything that must be trusted server-side |
| Security auditor | Walks the code against the cited specification sections |
| Compliance officer | Uses the section citations as the map from requirement to implementation |
| Solutions architect | Decides which protocol work belongs in the SDK versus the app |

## 7. KPIs / Success Criteria

| KPI | Signal |
|---|---|
| Spec traceability | 100% of protocol functions cite a specification section |
| Integration time | A new vertical reaches a working issuance round in days |
| Interoperability | The SDK passes the OIDF wallet-role conformance plans |
| Safety | Zero protocol paths that disclose a claim without a verified request |

## 8. Dependencies & Reliance on Other Services

| Dependency | Why |
|---|---|
| The platform SDK (`sentin8`) | The mdoc wire engine, crypto primitives, and shared types |
| Sentin8:Auth | The issuer and verifier surfaces the client talks to |
| The holder service | The composition point: orchestration and persistence |

## 9. Acceptance Criteria

1. A consumer workspace depends on the facade only and compiles.
2. An mdoc credential can be issued and presented through the SDK.
3. An SD-JWT VC credential can be issued and presented through the SDK.
4. A request object with a bad signature is rejected before any claim is
   read.
5. An unsatisfiable DCQL query produces the prescribed error, never an
   empty presentation.
6. Encrypted responses round-trip correctly.

## 10. Open Business Questions

- Whether the SDK should expose a higher-level "wallet session" API that
  hides the multi-round choreography, or keep the explicit round-by-round
  surface that mirrors the specification.
- Whether the Rust and Flutter SDKs should publish a shared conformance
  test corpus for third-party implementers.

