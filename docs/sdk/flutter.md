---
sidebar_position: 2
---

# wallet8 Flutter SDK - what a branded wallet embeds

The Flutter wallet SDK: the credential store, the OID4VCI and OID4VP
drivers, and the status sync. A vertical ships its own branded wallet by
embedding this package - it does not rebuild credential protocols.

## What it provides

| Piece | Role |
|---|---|
| Credential store | Holds credentials on the device, sealed with the platform's crypto primitives |
| OID4VCI driver | Drives the issuance: the authorize round, the session-based consent flow, the deferred polling |
| OID4VP driver | Drives the presentation: the request scan, the consent preview, the response delivery |
| Status sync | Keeps revocation state current from the Token Status List |
| DC API integration | Bridges the browser's Digital Credentials API into the same consent flow |

## The consent contract

The driver never presents without the app calling the consent surface
first. The consent view shows the exact claim set the verifier requested,
derived from the DCQL query - not a summary the app wrote. A caller cannot
bypass the preview through the SDK.

## Native and web from one package

The same package runs on iOS, Android, and web. Device key material is
created and used through the platform keystore; on web the DC API shim
supplies the browser-mediated channel. The protocol behavior is identical
on every target, so a certification run on one target describes all of
them.

## Embedding it in a vertical

1. Depend on the package.
2. Configure the holder backend URL for your environment (through the
   platform's derived domain).
3. Mount the vault, intake, consent, and outcome surfaces in your shell.
4. Brand it: the package takes your accent and logo; the protocol layer is
   unchanged.

## Full documentation

- [Flutter SDK reference](/docs/reference/flutter/) - the harvested repository docs.
