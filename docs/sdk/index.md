---
sidebar_position: 1
---

# SDKs

Two SDKs, one wallet: the Rust libraries any vertical's backend composes,
and the Flutter package any vertical's branded wallet app embeds.

| SDK | Language | Consumer |
|---|---|---|
| [wallet8 Rust SDK](/docs/sdk/runtime) | Rust | a vertical's backend service, the holder, server-side verification |
| [wallet8 Flutter SDK](/docs/sdk/flutter) | Dart / Flutter | the wallet app itself - native and web |

## Why two

The split follows the trust boundary, not fashion. Protocol work that must
be trusted by a backend (request verification, proof construction, response
decryption, status evaluation) belongs in the Rust SDK, where it runs behind
the platform's authorization pipeline. Protocol work that must run on the
user's device (holding keys, driving a consent screen, polling a deferred
issuance) belongs in the Flutter package, where the key material never
leaves the device.

## Shared invariants

Both SDKs implement the same section-by-section specification map, so a
behavior a conformance suite verifies for the Rust client holds for the
Flutter client too. Neither SDK stores a credential in plaintext, neither
signs a proof with a key it did not create, and neither presents a claim
the consent screen did not show.
