# Wallet8:Site - Business Requirements & Functionality

> Audience: product owners, business analysts, and anyone evaluating what
> Wallet8:Site **does for the business**. For architecture read
> `README.md`; for wire-level detail read `DESIGN.md`.

## 1. Business Purpose

Wallet8:Site is the **public face of the digital-wallet vertical**
(wallet8.dev). It delivers:

- **The buyer's evaluation surface** - what a wallet is in this stack,
  what the holder does, what the two SDKs give an integrator, and which
  standards the product implements, so a bank, hotel group, or health
  provider can evaluate Wallet8 without reading code.
- **The compliance story in business language** - the standards baseline
  (OID4VCI 1.0, OID4VP 1.0, SD-JWT VC, ISO 18013-5/-7, Token Status
  List, HAIP) presented so a compliance review can start from the site.
- **The vertical-embedding story** - how another vertical ships a branded
  wallet by composing Wallet8 instead of building credential protocols.
- **The developer reference** - the integration guide and samples for
  the two rounds every wallet must complete: issuance and presentation.
- **Always-truthful reference material** - harvested from the unit
  repositories at build time, so the site cannot overpromise.

## 2. Business Scope

| # | Capability area | What the business gets |
|---|---|---|
| 1 | Landing page | Positioning: the wallet role of the credential stack, the five wallet invariants, the product inventory |
| 2 | Product page | Wallet8:Holder - the backend, what it owns, its orchestration |
| 3 | App page | Wallet8:Wallet - the five certification surfaces, the DC API shim |
| 4 | SDK pages | The Rust and Flutter SDKs, what each provides, why they are split |
| 5 | Security page | The wallet invariants plus the inherited platform pillars, in business language |
| 6 | Developers page | Quickstart, the two integration shapes, repository map |
| 7 | Samples page | The issuance round and the presentation round |
| 8 | Reference section | The harvested per-repository docs |
| 9 | Family showcase | Cards routing to sentin8.dev and bank8.dev |
| 10 | Legal pages | Privacy Policy and Terms of Service at `/privacy` and `/terms` |
| 11 | Search | Local search over the full content set |

**What the site does NOT own (business boundary):**

| Out of scope | Owned by |
|---|---|
| The shared theme, components, and harvester | `sentin8-site-engine` |
| Unit documentation content | each `wallet8-*` repository |
| The issuer and verifier roles | Sentin8:Auth |
| Product behavior and standards compliance | the Wallet8 unit repositories |

## 3. Business Capabilities

| Capability | Business rule |
|---|---|
| Publish the product inventory | Every shipped unit appears on the landing page; a unit is added by editing the inventory, never by hand-editing HTML |
| Publish per-unit detail | Each unit has a hand-authored overview plus the harvested repository docs |
| Publish the standards baseline | The security and overview pages name the standards the product implements, section by section |
| Route to the family | Showcase cards link to the platform and to the vertical that is Wallet8's first embedding customer |
| Stay truthful | Harvested content is re-rendered from the repositories on every build |

## 4. Business Events (Outbound)

None. The site emits no events: it is a static build artifact. Content
changes are signaled by repository commits, which trigger the site's CI
lane.

## 5. Business Rules & Invariants

1. No capability is advertised that a unit repository does not implement.
2. No unit's documentation is duplicated on the site; it is harvested.
3. The site carries no personal data of its own.
4. Every footer legal link resolves to a real page.
5. The site builds from public sources only.

## 6. Target-User Roles & Use Cases

| Persona | What they do with the site |
|---|---|
| Decision maker (bank, hotel group, health provider) | Evaluates whether Wallet8 fits before contacting the team |
| Compliance officer | Reads the standards baseline and the security invariants |
| Integrator developer | Follows the developers page and samples, then the SDK references |
| Vertical product owner | Learns how to ship a branded wallet by composing Wallet8 |
| Existing customer | Checks revocation behavior, supported formats, and API surfaces |

## 7. KPIs / Success Criteria

| KPI | Signal |
|---|---|
| Time to first understanding | a visitor reaches the product inventory and the standards list from the landing page in one scroll |
| Build reliability | the CI lane builds green on every push to `main` |
| Content freshness | zero drift between the site and the unit repositories (harvested at build time) |
| Route integrity | zero broken links (the build fails otherwise) |

## 8. Dependencies & Reliance on Other Services

| Dependency | Why |
|---|---|
| `@sentin8/site-engine` | the theme, components, config factory, harvester, and scaffolder |
| The Wallet8 unit repositories | every product, app, and SDK page and its harvested docs |
| GitHub Pages | hosting and TLS termination for the marketing site |
| Sentin8 CI (`site.yml`) | the build and deploy lane |

## 9. Acceptance Criteria

1. `wallet8.dev` serves the landing page with the eyebrow, positioning
   title, and long description from the inventory.
2. The product, app, and both SDK pages exist and link to their
   harvested reference pages.
3. The security page states the wallet invariants and the inherited
   pillars.
4. The developers and samples pages describe the two rounds.
5. `/privacy` and `/terms` resolve.
6. The family showcase links to sentin8.dev and bank8.dev.
7. `npm run build` completes with zero broken links.
8. Wallet8 appears in the navbar dropdown and footer of every sibling
   site in the family.

## 10. Open Business Questions

- Should the site publish the OIDF conformance results as a certification
  statement once the wallet-role plans pass in CI?
- Should the site offer a hosted demo wallet, or link only to self-hosted
  instructions?
