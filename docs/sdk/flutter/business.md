---
title: "wallet8 Flutter SDK - Business rules (BRD)"
sidebar_label: "Business rules (BRD)"
mdx:
  format: md
custom_edit_url: https://github.com/Wallet8-dev/wallet8-flutter/edit/main/BUSINESS.md
---
# wallet8 Flutter SDK - Business Requirements & Functionality

> Audience: product owners, business analysts, and app developers
> evaluating what the **wallet8 Flutter SDK** does for the business. For
> architecture read `README.md`; for the vertical's standards baseline read
> the `wallet8` repository's `BUSINESS.md`.

## 1. Business Purpose

This package is the reason a vertical can ship a branded wallet without
becoming a protocol company. It packages everything that must run on the
user's device - holding keys, keeping credentials, driving a consent
screen - behind a small, stable API.

It exists so that:

- **A branded wallet is an integration, not a project.** A bank embeds the
  package in its own app, applies its own brand, and gets issuance and
  presentation.
- **Key material stays on the device.** The private key is created and used
  in place; nothing about the user's wallet leaves the phone or browser
  except the presentation the user approved.
- **The consent contract cannot be bypassed.** The package will not present
  before the app has shown the requested claim set, so a hastily built
  screen cannot quietly over-disclose.
- **Native and web behave identically.** One package, one protocol
  behavior, so a certification run on one target describes them all.

## 2. Business Scope (What the Package Provides)

| # | Capability | What the business gets |
|---|---|---|
| 1 | Credential store | The user's credentials listed and held on the device |
| 2 | Device key management | A key created and used on-device; only the public half is ever registered |
| 3 | OID4VCI driver | Offer intake, the authorize round, proofs, deferred polling |
| 4 | OID4VP driver | Request resolution, the consent preview, delivery of the approved presentation |
| 5 | Status sync | Revocation state kept current so a revoked credential stops presenting |
| 6 | DC API integration | The browser's native credential mediation plugged into the same consent flow |
| 7 | BFF-only backend access | Every backend call routes through the platform BFF; no tokens in the app |

**What the package does NOT own (business boundary):**

| Out of scope | Owned by |
|---|---|
| Server-side protocol verification and response decryption | `wallet8-sdk` (Rust), running in the holder |
| Credential persistence as the system of record | `Wallet8:Holder` |
| The consent screen's visual design | the embedding app |
| Identity, tokens, session management | Sentin8:Auth + Sentin8:BFF |
| The issuer and verifier roles | Sentin8:Auth |

## 3. Business Capabilities

| Capability | Business rule |
|---|---|
| Intake an offer | The offer is untrusted until the backend re-validates it; the app starts the flow, the holder adjudicates it |
| Show the consent preview | The claim set comes from the resolved request, not from a string the app wrote |
| Present | The app must approve explicitly; the package cannot present on its own initiative |
| Deferred issuance | A pending issuance is polled without asking the user to start over |
| Status sync | The vault's revocation state is refreshed on the app's schedule |
| Branding | The app supplies accent, logo, and shell; the protocol layer is untouched |

## 4. Business Events (Outbound)

None. The package is a client library; it produces no domain events. The
holder service emits the domain events for the operations the app
triggers.

## 5. Business Rules & Invariants

1. **Consent is a precondition, not a step.** No presentation is delivered
   without a preceding resolution the app displayed.
2. **The private key never leaves the device.** Only the public half is
   registered with the holder.
3. **The app never holds a bearer token.** Backend access goes through
   the BFF client.
4. **A revoked credential stops presenting** in the app, before the
   verifier is contacted.
5. **One behavior on every target.** Native and web use the same protocol
   logic.
6. **No credential in plaintext storage.** Local persistence uses the
   platform's sealing primitives.

## 6. Target-User Roles & Use Cases

| Persona | Use case |
|---|---|
| Vertical app developer | Embeds the package, mounts four screens, brands them, ships a wallet |
| Wallet end user | Scans an issuer offer, receives a credential, presents at a verifier |
| Product owner | Confirms the app's consent surface is the SDK's consent surface, not a hand-rolled one |
| Security reviewer | Verifies key material never leaves the device and tokens never reach the app |

## 7. KPIs / Success Criteria

| KPI | Signal |
|---|---|
| Embedding time | A vertical reaches a working issuance and presentation flow in days |
| Consent integrity | Zero presentations without a displayed claim set |
| Device binding | Only public keys ever reach the backend |
| Cross-target parity | The same protocol behavior on iOS, Android, and web |

## 8. Dependencies & Reliance on Other Services

| Dependency | Why |
|---|---|
| `Wallet8:Holder` | The backend that adjudicates issuance and presentation |
| `wallet8-sdk` (Rust) | Runs server-side where the holder composes it |
| Sentin8:BFF | The only backend path; tokens stay server-side |
| The platform Flutter SDK (`sentin8`) | The BFF client and the shared shell primitives |

## 9. Acceptance Criteria

1. The app lists the user's credentials from the holder.
2. An issuer offer scanned by the app lands the credential in the vault.
3. A verifier request resolves to a consent preview showing the requested
   claims.
4. Declining produces `access_denied`, never an empty presentation.
5. A revoked credential no longer appears as presentable.
6. The same flow works on web and on a native target.

## 10. Open Business Questions

- Whether the package should ship a default consent UI that verticals can
  restyle, or continue to require the app to supply one.
- Whether device-to-device credential migration belongs in this package or
  in the holder.

