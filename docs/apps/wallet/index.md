---
sidebar_position: 1
---

# Wallet8:Wallet - the reference wallet app

The reference Flutter web wallet: the five certification surfaces plus
the W3C Digital Credentials API shim. It is thin by design - the protocol
drivers live in the SDKs, the persisted state lives in Wallet8:Holder, and
every backend call goes through Sentin8:BFF so tokens never reach the
client.

## The five surfaces

| Surface | What it does |
|---|---|
| Vault | Lists the credentials the holder holds, with issuer, format, and status |
| Scan intake | Accepts an issuer offer or a verifier request by QR or deep link |
| Consent preview | Shows the exact claim set requested before anything is presented |
| Error screen | States the failure in protocol terms (expired offer, unsupported format, status failure) |
| Outcome landing | Confirms what was presented, to whom, and when |

## The DC API shim

`web/dc_api_shim.js` implements the W3C Digital Credentials API bridge:
the origin-bounded promise the browser hands to a verifier, and the
`dc_api` / `dc_api.jwt` response channel the wallet answers on.

## What it demonstrates

The app is the executable definition of the wallet-role certification
contract: the OIDF wallet plans (`oid4vci-1_0-wallet`, `oid4vp-1final-wallet`
and their `-haip` variants) drive it, so every surface exists because a
conformance step exercises it.

## Full documentation

- [Reference](/docs/reference/wallet/) - the harvested repository docs.
