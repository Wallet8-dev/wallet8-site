---
title: Privacy Policy
description: How Wallet8, the wallet8.dev website, and the inherited Sentin8 platform handle personal data.
---

# Privacy Policy

Wallet8 is the wallet product line of the Sentin8 platform for digital
credentials. This policy explains what personal data Wallet8, the
wallet8.dev website, and the underlying Sentin8 platform process, why,
and the guarantees that protect it.

## The short version

- The wallet8.dev website is fully static. It sets **no cookies**, embeds
  **no third-party trackers**, and sends **no analytics** about your visit
  anywhere.
- Wallet8 deployments run on the Sentin8 platform. The operating tenant is
  the **data controller** for the personal data in its environment; the
  platform stores that data only inside the tenant's own database, isolated
  by mandatory row-level security.
- We do not sell, rent, or share personal data with third parties for
  marketing or advertising. Ever.

## What Wallet8 processes

Wallet8 services run as part of a Sentin8 deployment operated by a tenant
organization. The tenant is the data controller for the personal data
inside its environment.

| Data | Where it lives | Who controls it |
|---|---|---|
| Wallet credentials (verifiable credentials held by a user) | The tenant's holder vault, sealed with AES-256-GCM | The tenant |
| Device keys (public halves only) | The tenant's holder database | The tenant |
| Presentation history (which verifier saw which claims, when) | The tenant's holder database | The tenant |
| Website visit logs | Not collected by this site | - |

## Credentials are sealed at rest

A credential is stored as an encrypted envelope. The plaintext credential
never exists in an application table, and the sealing key derives from the
tenant's own service secret - so a database copy without that secret
yields no readable credential.

## Your rights

Depending on your jurisdiction you may have the right to access, correct,
export, or delete the personal data a tenant holds about you. Contact the
tenant operating the environment; the platform provides the mechanisms and
the tenant exercises them. Data-subject requests are handled by the tenant,
not by this website.

## Changes

Material changes to this policy are published on this page. The site is
built from its repository, so the history of the policy is the commit
history.
