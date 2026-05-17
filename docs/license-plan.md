# Disrupted Studio — Commercial License Plan

**Status:** Phase 1 in progress
**Last reviewed:** 2026-05-17
**Owner:** Blaine

This is the master strategy doc for tiered commercial licenses across all
product types. Derived from research on Studio 2am, Blkmarket,
Mass-Driver, Creative Market, Klim, Dinamo, OH no Type, Pangram Pangram,
and Envato. See bottom of doc for source URLs.

---

## Headline strategy

Mirror Studio 2am's tier structure but **specialize per product type**,
keep existing prices as the "Standard Commercial" floor (do **not** demote
existing customers to "Personal"), ship the license-terms infrastructure
before launch, layer tiered pricing on top later.

Serious indie shops (Studio 2am, Mass-Driver, Blkmarket) don't sell a
"Personal" tier — everything is commercial by default, and the upsell is
to *Extended*. Following that consensus avoids retroactively downgrading
the value proposition for existing customers.

---

## Per-product-type strategy

The single biggest insight: **fonts, graphics, and plugins gate on
completely different things**. A unified license tier across all three is
the wrong abstraction. Studio 2am bifurcates exactly this way.

The already-typed product taxonomy (`product.plugin.json` /
`product.font.json` / `product.graphics.json` / `product.bundle.json`) is
the right substrate for per-type tiers.

### Plugins (BACKLIT, Pixel Perfect, future)

Per-seat model. End-product count is meaningless — plugins help make art,
they don't *become* the art.

| Tier         | Seats          | Price       |
|--------------|----------------|-------------|
| **Standard** | 1 designer     | Current     |
| **Studio**   | up to 5 seats  | 2–2.5×      |

Two tiers only. Bigger usage = email Blaine for a custom Draft Order
(Mass-Driver removed enforcement granularity for sanity).

### Fonts (Neue Portal, Rivet Mono, future)

Bundled usage tiers. Studio 2am's Desktop/Web/ePub/App axis is the indie
standard, but flat "Extended unlocks everything" is cleaner at this SKU
count.

| Tier         | Includes                                       | Price   |
|--------------|------------------------------------------------|---------|
| **Standard** | Desktop only (.otf / .woff for design files)   | Current |
| **Extended** | Desktop + Web + ePub + App embedding           | 2.5–3×  |

No MAU / pageview brackets — they're unenforceable at this scale (Klim,
Mass-Driver, Dinamo all moved away from them).

### Graphics packs (Chroma Vol 01, Botanic, future)

End-product count + resale rights. Direct Studio 2am / Blkmarket mirror.

| Tier         | End products for sale | Sublicensing                       | Price   |
|--------------|-----------------------|------------------------------------|---------|
| **Standard** | up to 10,000          | Not allowed                        | Current |
| **Extended** | up to 250,000         | In end product only (not standalone)| 2–3×    |

The 10K / 250K numbers come straight from Studio 2am and Blkmarket — using
them puts the brand in conversation with a market customers already
understand.

### Bundles

Inherit highest tier of any component. Merchant picks one tier for the
whole bundle. No mix-and-match per item.

---

## Pricing multipliers

Concrete numbers, applied to whatever the Standard price is:

| Product type | Upsell tier | Multiplier | Rationale                                  |
|--------------|-------------|------------|--------------------------------------------|
| Plugin       | Studio      | ×2.0       | 5 seats at 40% off each = volume discount  |
| Font         | Extended    | ×2.5       | Creative Market floor + indie norm         |
| Graphics     | Extended    | ×2.5       | Indie consensus; 5× would be predatory     |
| Bundle       | Extended    | ×2.5       | Consistency with graphics                  |

Worked examples:

- **BACKLIT** $48 Standard → **$96 Studio**
- **Pixel Perfect** $19 Standard → **$39 Studio**
- Hypothetical font at $40 → **$99 Extended**
- Hypothetical graphics pack at $32 → **$79 Extended**

Push Extended to 3–4× only if positioning premium (Mass-Driver territory).

---

## License terms infrastructure

Every marketplace studied has a fixed-URL license page buyers can
reference. Pangram Pangram's clause is the legal foundation: *"the
version of this License that is applicable for each [product] is that in
effect at the date and time at which the Licensee ordered."*

**Therefore: version the license page. Store current terms, never delete
old versions.**

### Required clauses (minimum enforceable EULA — 11-clause checklist)

1. Definitions (Licensee, Software/Font/Asset, Standard, Extended)
2. License grant — perpetual, non-exclusive, non-transferable
3. Permitted uses per tier
4. Prohibited uses (redistribution, sublicensing except Extended Graphics,
   AI training, source-file sharing)
5. Seat / end-product / usage limits per type
6. Permitted formats / embedding
7. Refund clause (all sales final + EU 14-day digital-goods exception)
8. Termination (breach → license terminates, files must be deleted)
9. Disclaimer of warranties (as-is)
10. Governing law
11. Version date + changelog

### Affirmative accept, not browse-wrap

Browse-wrap EULAs have "a reputation for being hard to enforce" (Society
of Fonts, 2019). Use Shopify's native "Terms of service" checkbox setting
on the cart (Settings → Checkout → I agree to the terms of service)
linked to `/pages/license`.

---

## Shopify implementation phases

### Phase 1 — Terms infrastructure (DONE)

Pure groundwork. No pricing changes. Every product still reads as
commercial-by-default.

- [x] Create `templates/page.license.json`
- [x] Create `sections/ds-license-terms.liquid` (brutalist tier comparison
      + numbered terms body + version badge + changelog)
- [x] Link the existing PDP `License: …` row to `/pages/license`
- [x] Custom cart consent gate — checkbox above Checkout button on both
      drawer and cart page (replaces the missing Shopify Plus / Checkout
      Extensibility checkbox). See `snippets/ds-cart-agreement.liquid`.
- [ ] (Admin task — Shopify dashboard) Create a Page "License" assigned
      to `page.license` template; populate the section settings
- [ ] (Admin task) Settings → Policies → Terms of service: paste link to
      `/pages/license` (provides browse-wrap fallback for dynamic checkout
      buttons — Apple Pay, Shop Pay, etc. — which bypass the custom gate)
- [ ] (Admin task) Edit order confirmation email to mention license + URL
      (Settings → Notifications → Order confirmation)

### Phase 2 — Tier-as-variant rollout (per product, opt-in)

Done one product at a time. Existing variant select infrastructure on PDP
takes the upgrade with metafield-driven labels.

**Theme code — DONE for all products at once (graceful fallback):**

- [x] PDP variant select now detects `disrupted.tier_name` variant
      metafield. When present on any variant, label switches to "License
      tier" and option text becomes "[Tier] — $XX / [tier_summary]"
- [x] "Compare tiers + read full license" link below variant select on
      tiered products, opens `/pages/license` in new tab
- [x] Graceful fallback: PDPs without tier metadata render exactly like
      before — no visual change until merchant adds the metafields

**Admin checklist — Pixel Perfect (first product to tier):**

Variants:
- [ ] Admin → Products → Pixel Perfect Plugin
- [ ] Edit the product. Under "Variants," add an option named "License"
      with two values: "Standard" and "Studio"
- [ ] Set prices: Standard = $19.00, Studio = $39.00 (×2 multiplier per
      pricing table above)
- [ ] Both variants should share the same SKU prefix and the same
      inventory tracking settings (digital → unlimited)
- [ ] Save

Metafield definitions (one-time setup, applies to all products):
- [ ] Settings → Custom data → Variants → Add definition
- [ ] Name: "Tier name" · Namespace and key: `disrupted.tier_name` ·
      Type: Single line text
- [ ] Add another definition. Name: "Tier summary" · Namespace and key:
      `disrupted.tier_summary` · Type: Single line text
- [ ] Save the definitions

Per-variant metafield values (back on the Pixel Perfect product):
- [ ] Click the Standard variant. Scroll to "Metafields" section. Set:
      - `tier_name` = `Standard`
      - `tier_summary` = `1 seat`
- [ ] Click the Studio variant. Set:
      - `tier_name` = `Studio`
      - `tier_summary` = `Up to 5 seats`
- [ ] Save

Verify on the live PDP:
- [ ] Refresh `/products/pixel-perfect-plugin`
- [ ] Picker label should now read "License tier" (not "License")
- [ ] Options should read "Standard — $19.00 / 1 seat" and
      "Studio — $39.00 / Up to 5 seats"
- [ ] Switching variants in the dropdown should update the Add-to-cart
      button price (Horizon's existing variant-sync JS handles this)
- [ ] "Compare tiers + read full license" link appears below the dropdown
- [ ] Add to cart → drawer shows the selected variant + tier in the
      line item title

**Repeating for BACKLIT / fonts / graphics later:**

Same flow. Variants + per-variant metafields. The theme code already
handles every product type — no per-product code changes needed.
Reference the multiplier table at the top of this doc for prices.

### Phase 3 — Cart upsell (DONE for drawer)

Pivoted from the originally-planned PDP nudge to a cart-level upsell.
The tier cards on the PDP already show both options side-by-side, so a
"Upgrade?" nudge below the ATC button would have been redundant. Cart
upsell catches the post-commit "should I have picked the upper tier?"
moment instead.

- [x] Detect upgrade target: cheapest available variant of the same
      product priced above the cart line item
- [x] Render brutalist dashed-border button inside `.ds-cart__item`,
      spanning all grid columns — "↑ UPGRADE · to Commercial · +$20.00 →"
- [x] Click handler: POST `/cart/change.js` (quantity: 0) to remove the
      lower-tier line, then POST `/cart/add.js` for the upgrade variant,
      then reload to refresh cart UI. SessionStorage flag re-opens the
      drawer on the next page load so the user sees the upgraded cart
      immediately without re-clicking the cart icon.
- [x] Loading state during round-trip: dashed border button shows
      "— updating…" suffix, disabled to prevent double-clicks.
- [x] Idempotent script init (window.__dsCartUpsellInit) so listeners
      attach once even when the snippet renders in two places.

**Coverage:** drawer only for now. The cart page (templates/cart.json
→ main-cart) uses Horizon's default `_cart-products` block which
renders Horizon's snippets/cart-products.liquid, not the customized
ds-cart-products. To add upsell to the cart page too, the simplest
move is to swap the `_cart-products` block in main-cart.liquid for
`{% render 'ds-cart-products' %}` so both surfaces share the custom
snippet. Deferred — the drawer is the primary surface (hero ATC opens
it directly).

### Phase 4 — Customer accounts + license retrieval (post-launch)

No `templates/customers/` yet. When accounts ship, build
`/account/licenses` listing every order's tier with link back to
`/pages/license`. Creative Market, Future Fonts, Envato all rely on this
pattern.

### Phase 5 — Pay-the-difference upgrades

Manual via Contact form. Grilli Type / Foundry Types model. Email →
custom Draft Order in Shopify. No automation until volume justifies (it
won't for a long time).

---

## Edge cases — resolved

| Edge case                                  | Resolution                                                                                                 |
|--------------------------------------------|------------------------------------------------------------------------------------------------------------|
| Existing customers when tiers launch       | Grandfather to Standard. Version-at-purchase clause covers retroactive changes.                            |
| Agency buying for client                   | Issue license in client's name. Add optional "License recipient" field at cart.                            |
| Refund requests                            | All sales final + EU 14-day digital-goods exception. Don't try to revoke once downloaded.                  |
| License upgrade post-purchase              | Manual Contact form → Draft Order for difference. Pay-the-difference (Grilli Type model).                  |
| Multi-seat verification                    | Trust-based. Don't try to enforce — Mass-Driver explicitly removed seat enforcement.                       |
| Lost license / re-issue                    | Customer logs into account (post-Phase 4) or emails support → resend order confirmation.                   |
| License transfer to new owner              | Non-transferable. Exception: sole proprietor sells business → email-only transfer at no charge.            |
| AI training use                            | Explicitly prohibited in license terms. Required for 2026.                                                 |
| Plugin redistribution / cracking           | Prohibited in terms; rely on order trail. License keys overkill until evidence of piracy hurting sales.    |
| Bundle license tier mismatch               | Bundle PDP states "this bundle is licensed under [tier]". No per-item mixing.                              |
| Font in client logo                        | Standard covers it. Studio 2am, Mass-Driver both allow logos under Standard.                               |
| Buyer dispute / chargebacks                | Shopify handles. License terminates on chargeback per termination clause.                                  |
| License terms change after sale            | Customer keeps version they bought under. Versioned page with archive links.                               |

---

## What NOT to build

Three clear signals from research:

1. **Impression / pageview / MAU enforcement.** Fontspring: "basically
   impossible to accurately count and enforce." Klim only does it on
   Advertising tied to a declared media buy. At this scale: never.
2. **PDF stamping.** SendOwl and EDP do it ($20–40/mo). Order # in email
   + versioned license page is enough proof at <100 orders/week.
3. **License keys for plugins.** Until BACKLIT hits 1000+ sales with
   evidence of piracy hurting sales, license keys (LicenseSpring,
   Digitally) are over-engineering. UXP plugins are hard to redistribute
   meaningfully anyway.

---

## Reference: marketplaces studied

- Studio 2am license: https://studio2am.co/pages/license
- Studio 2am help — licenses: https://help.studio2am.co/articles/licenses
- Blkmarket license: https://blkmarket.com/license/
- Mass-Driver V2 announcement: https://mass-driver.com/article/simpler-font-licensing-introducing-v2
- Creative Market license FAQ: https://creativemarket.com/licenses
- Klim licences: https://klim.co.nz/licences/
- Dinamo pricing essay: https://abcdinamo.com/news/about-our-pricing
- OH no Type web license: https://ohnotype.co/info/licenses/web
- Pangram Pangram EULA: https://pangrampangram.com/pages/eula
- Grilli Type info (upgrade policy): https://www.grillitype.com/information
- Society of Fonts EULA anatomy: https://www.societyoffonts.com/2019/03/22/eula-part-1the-anatomy-of-an-end-user-license-agreement/
- Fontspring's "good licensing" essay: https://blog.fontspring.com/2016/11/how-were-making-good-font-licensing-great/

## Reference: indie-shop quotes that shaped this plan

- **Mass-Driver V2:** pricing is based on "the size of the company
  licensing them, with small companies paying less and big companies
  paying more... they don't charge extra for using fonts on the web or
  other media, and they don't require you to upgrade a license if your
  usage changes later."
- **Dinamo:** "font licenses should be owned and paid for by the client
  for whom the fonts are being used — not by the designers working for
  them."
- **Pangram Pangram:** "Sales are final, and no refunds or credits may be
  asked of the Licensor on payments made for access to the Fonts."
- **Grilli Type:** "You only ever pay for the upgrade — the difference
  between your previous licensing cost and the larger licensing tier."
- **Fontspring on impression-based licenses:** "basically impossible to
  accurately count and enforce."
