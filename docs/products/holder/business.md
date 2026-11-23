---
title: "Wallet8:Holder - Business rules (BRD)"
sidebar_label: "Business rules (BRD)"
mdx:
  format: md
custom_edit_url: https://github.com/Wallet8-dev/wallet8-holder/edit/main/BUSINESS.md
---
# Wallet8:Holder - Business Requirements & Functionality

> Audience: product owners, business analysts, and anyone evaluating what
> Wallet8:Holder **does for the business**. For architecture read
> `DESIGN.md`; for the vertical's standards baseline read the
> `wallet8` repository's `BUSINESS.md`.

## 1. Business Purpose

Wallet8:Holder is the backend of the wallet. It is the only component
that persists wallet state, and therefore the only component that can
answer the questions a wallet must answer: *what credentials does this
user hold, were they obtained legitimately, and what have they
disclosed?*

It exists for four business reasons:

- **Guarantee the vault.** A credential is an asset. The holder is the
  system of record for that asset and stores it sealed - never in
  plaintext, never in a log, never in an API response.
- **Own the protocol choreography.** Issuance and presentation are
  multi-party, multi-round exchanges with redirects, nonces, deferred
  responses, and callbacks. Doing that in a browser app would put
  protocol state on the user's device; the holder keeps it server-side.
- **Produce the audit trail.** OID4VP 1.0 places an audit duty on the
  wallet: it must be able to say who saw which claims, when. The holder
  is that record.
- **Keep the app thin.** The reference wallet renders surfaces; the
  holder holds judgment. That split is what lets a vertical ship a
  branded wallet without touching protocol logic.

## 2. Business Scope (What the Service Provides)

| # | Capability | What the business gets |
|---|---|---|
| 1 | Credential vault | The user's credentials, sealed with AES-256-GCM at rest, one slot per environment and user |
| 2 | Device-key registry | One device key per wallet, registered by its public half; idempotent re-registration by thumbprint |
| 3 | Issuance intake | Resolves an issuer's Credential Offer into a usable issuance session |
| 4 | Authorization-code orchestration | `prepare` starts the round, the browser completes it, `complete` finishes and stores |
| 5 | Deferred issuance | Polls an issuer's deferred endpoint on schedule until the credential is ready |
| 6 | Presentation resolution | Verifies the request object and returns the exact claim set the verifier asked for, before anything is disclosed |
| 7 | Presentation delivery | Delivers the approved presentation over `direct_post`, the encrypted form, or the DC API channel |
| 8 | Presentation history | Records every verification event: which verifier, which claims, when |
| 9 | Status sync | Walks the Token Status List so a revoked credential stops matching requests |
| 10 | Admin surfaces | RFW pages for the vault browser, device keys, and presentation history |

**What the service does NOT own (business boundary):**

| Out of scope | Owned by |
|---|---|
| Protocol client logic (request verification, proof construction, response decryption) | `wallet8-sdk` (Rust) and `wallet8-flutter` |
| The issuer role | Sentin8:Auth |
| The verifier role | Sentin8:Auth |
| The mdoc wire engine | the platform SDK (`sentin8::mdoc`) |
| The user-facing screens and consent UX | `wallet8-wallet` |
| Identity, tokens, tenant resolution | Sentin8:Auth + Sentin8:BFF |
| Sealing keys | Sentin8:Secrets |

## 3. Business Capabilities

### Vault

| Capability | Business rule |
|---|---|
| Store a credential | The material is sealed before it touches a table; the response carries the row view, never the material |
| List credentials | Only the display metadata returns: format, doctype/vct, display name, issuer, status, issued-at |
| Retrieve one credential | Scoped to the requesting user in the requesting environment |
| Renew a credential | A re-issued credential replaces the holding in place; the vault is a slot, not an append-only log |

### Device binding

| Capability | Business rule |
|---|---|
| Register a key | Only the public JWK is accepted; the RFC 7638 thumbprint is the registry identity |
| Re-register a key | Same thumbprint in the same environment is idempotent, not a duplicate |

### Issuance

| Capability | Business rule |
|---|---|
| Intake an offer | The offer is untrusted input; every later step re-validates against re-resolved issuer metadata |
| Prepare an authorization | The round is started server-side and its session returned to the app |
| Complete an authorization | The proof is built with the registered device key; the credential is sealed into the vault on success |
| Deferred | A pending response parks the transaction; the poll resumes it without user re-entry |

### Presentation

| Capability | Business rule |
|---|---|
| Resolve a request | Verify the request object first; a bad signature, a missing nonce, or an unsatisfiable query fails before any claim is shown |
| Consent | The resolved claim set is what the user sees; nothing is disclosed without approval |
| Deliver | A decline answers `access_denied`; an unsatisfiable request answers the prescribed error - never an empty or partial `vp_token` |
| Encrypted responses | The `direct_post.jwt` form is delivered encrypted to the verifier's key |
| DC API | The browser-mediated channel answers the origin-bounded promise |

### Revocation and history

| Capability | Business rule |
|---|---|
| Status sync | Every active credential's status is checked; a revoked row flips to revoked and stops presenting |
| History | Every presentation event is recorded for the user's audit duty |

## 4. Business Events (Outbound)

| Event | Fires when |
|---|---|
| `wallet8:holder:credential:stored` | A vault intake completed and the credential is sealed |
| `wallet8:holder:credential:revoked` | A status sync flipped a credential to revoked |
| `wallet8:holder:presentation:recorded` | A presentation completed - who saw what, when |

## 5. Business Rules & Invariants

1. **Ciphertext-only persistence.** No plaintext credential material
   exists in any table, log, or response.
2. **One holding per user.** The vault slot is unique per (environment,
   user); renewal replaces rather than appends.
3. **Tenant isolation is enforced, not requested.** Every tenant table is
   RLS-forced and every query sets the tenant context on the same
   connection.
4. **Consent precedes disclosure.** Resolution and delivery are separate
   operations; there is no single call that decides and reveals.
5. **No value-leaking errors.** An error never reveals claim values; a
   mismatch is indistinguishable from a decline.
6. **No partial presentations.** An unsatisfiable required query fails
   the whole request.
7. **The private key never arrives.** Only public keys are registered.
8. **A revoked credential stops presenting**, in the wallet, before the
   verifier sees it.

## 6. Target-User Roles & Use Cases

| Persona | Use case |
|---|---|
| Wallet end user | Holds credentials, consents to presentations, reviews their own history |
| Vertical integrator | Points a branded wallet app at the holder and gets issuance and presentation without writing protocol code |
| Wallet administrator | Browses the vault, manages device keys and revoked credentials from the admin pages |
| Compliance officer | Reads the presentation history as the wallet-side audit record |
| Security auditor | Confirms ciphertext-only storage, RLS enforcement, and consent separation |

## 7. KPIs / Success Criteria

| KPI | Signal |
|---|---|
| Vault integrity | Zero plaintext credential material in any store, log, or response |
| Consent integrity | Zero presentations delivered without a preceding resolved claim set |
| Issuance reliability | A deferred issuance completes without user re-entry |
| Revocation latency | A revoked credential stops presenting at the next status sync |
| Audit completeness | Every completed presentation has a history row |

## 8. Dependencies & Reliance on Other Services

| Dependency | Why |
|---|---|
| Sentin8:Auth | Identity, tokens, the issuer and verifier roles the wallet talks to |
| Sentin8:BFF | The app's only backend path; tokens stay server-side |
| Sentin8:Secrets | The vault's sealing key is a secret reference, never an inline value |
| Sentin8:Bus | Delivers the outbound vault and presentation events |
| Sentin8:DB (PostgreSQL) | The `holder` schema in the vertical database |
| The platform SDK | The shell boot, the crypto envelope, and the mdoc engine |

## 9. Acceptance Criteria

1. A credential issued through the wallet is retrievable after a service
   restart with its material intact and sealed.
2. Listing credentials never returns credential material.
3. A second issuance for the same user replaces the vault slot.
4. A presentation cannot be delivered without a prior resolve.
5. A revoked credential no longer satisfies a request that previously
   matched it.
6. Every completed presentation appears in the history surface.
7. A device key re-registration with the same thumbprint is idempotent.

## 10. Open Business Questions

- Device-to-device wallet migration (export/import) is deferred; the
  business question is whether it belongs in the holder or in the app.
- Whether presentation history should support tenant-level retention
  policy configuration, or remain user-scoped only.

