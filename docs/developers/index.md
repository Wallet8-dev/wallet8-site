---
sidebar_position: 1
---

# Developers

Wallet8 is a Cargo workspace for the backend and SDK, and a Flutter package
for the app. This page is the shortest path from clone to a working wallet
flow.

## Quickstart

```bash
git clone https://github.com/Wallet8-dev/wallet8.git
cd wallet8
make build-rust
make start
```

The platform must be up first (Sentin8:Auth for the issuer and verifier
roles, Sentin8:BFF for the app's proxy path, Sentin8:Secrets for the vault's
data key). The vertical enrolls its services with the platform in the normal
way.

## The two integration shapes

### A vertical embedding the wallet

Depend on the SDKs, point the app at your holder, brand the surfaces. The
protocol layer is unchanged - that is the point of shipping the wallet as a
vertical.

### A backend that must verify a wallet

Use the Rust SDK's presentation client: verify the request object, resolve
the Client Identifier Prefix, select the claims, and decrypt the response.
The same functions the wallet uses to answer are the ones a backend uses to
check.

## Repository layout

| Repo | Contents |
|---|---|
| [wallet8-holder](https://github.com/Wallet8-dev/wallet8-holder) | the holder backend service |
| [wallet8-sdk](https://github.com/Wallet8-dev/wallet8-sdk) | the Rust SDK facade and crates |
| [wallet8-flutter](https://github.com/Wallet8-dev/wallet8-flutter) | the Flutter package |
| [wallet8-wallet](https://github.com/Wallet8-dev/wallet8-wallet) | the reference wallet app |
| [wallet8-e2e](https://github.com/Wallet8-dev/wallet8-e2e) | the Playwright harness |

## Next

- [Samples](/docs/samples) - the issuance round and the presentation round.
- [Security](/docs/security) - the guarantees every request inherits.
- [Products](/docs/products/holder) - the holder service in detail.
