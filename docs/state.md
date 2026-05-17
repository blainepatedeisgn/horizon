# Disrupted Studio — Project state (compaction)

> Persistent snapshot of where the theme port stands. Update when phases ship or major decisions land.

## Project
Porting `disrupted-studio-mockup.html` (single-file brutalist HTML/CSS/JS mockup) into a forked, upgrade-safe Shopify Horizon theme for **Disrupted Studio** — solo design-asset store. Launch product: **BACKLIT** (Photoshop plugin, $48). Brand: pure black bg, off-white text, orange accent `#FF4D1F`, Inter Tight + JetBrains Mono.

## Infrastructure

| Thing | Value |
|---|---|
| Repo | `~/Desktop/disrupted-studio-theme/` |
| Branch | `custom/disrupted-studio` |
| Dev store | `disrupted-studio.myshopify.com` (storefront password: `iadren`) |
| Dev preview | `http://127.0.0.1:9292` (hot-reload via `shopify theme dev` background process) |
| Unpublished admin theme | `Disrupted Studio` (ID `183438737681`) — sync via `shopify theme push --theme="Disrupted Studio" --nodelete --json` |
| Real merchant store (deferred until launch) | `disrupted-minds-studio.myshopify.com` — owner `blainepate2002@gmail.com` |
| Partners account | `blainepate2002@gmail.com` |
| GitHub fork | `blainepatedeisgn/horizon`, upstream `Shopify/horizon` |
| Mockup source of truth | `/Users/highcountryecomm/Downloads/disrupted-studio-mockup.html` |
| Original handoff spec | `/Users/highcountryecomm/Downloads/disrupted-studio-horizon-handoff.md` |

## Auth backstory (so we don't relive it)
Theme Access tokens were broken on the original merchant store (Shopify-side proxy sync bug). Pivoted to Partners + a free dev store. CLI uses Partners OAuth — re-auth via browser if a future command says "log in". `.env` has only `SHOPIFY_FLAG_STORE` + `SHOPIFY_FLAG_STORE_PASSWORD` — no token needed.

## Architecture pattern (the lesson learned)
Tried CSS-only restyle of Horizon vendor sections (per handoff spec) for PDP + header + footer. It fell short because Horizon composes those from many nested generic blocks with auto-generated classes.

**Switched to full custom replacement** for major surfaces: `ds-product-main`, `ds-header`, `ds-footer`. All custom files use the `ds-` prefix. Still using Horizon's variant picker / cart drawer / search modal **functionally** — but the visible shell is ours.

Per-product content is **metaobject-driven** so each product gets unique data without forking templates. Per-product *layout* is **template-driven** — `product.json` (default), `product.plugin.json`, `product.font.json`. Merchant picks per product in admin.

## Phase status

- ✅ **Phase 1**: Foundations — `ds-tokens.css`, color schemes, `settings_data.json` brutalist config. Committed.
- ✅ **Phase 2**: Homepage — hero, trust marquee, product grid + card snippet, category teasers, newsletter card. Wordmark moved into footer in Phase 4. All `index.json` wired. Committed.
- ✅ **Phase 3**: PDP — `ds-product-main` (custom, replaces Horizon's product-information), `ds-pdp-features`, `ds-pdp-faq` (metaobject-driven), `ds-pdp-cta-inset`, `ds-pdp-video`, `ds-pdp-related` (collection-based + fallback to `collections.all`), `ds-pdp-typespecimen` (live `@font-face` for font products), `product.plugin.json`, `product.font.json`. Committed.
- ✅ **Phase 4 Batches 1-3**: Custom header (sticky, mobile drawer, search/cart icons with labels) + custom footer (4-col, social, payment chips, wordmark) + section-head fixes (`03` superscript globally + view-all link pattern). Committed `34c70f8`. **Pushed to unpublished theme.**
- ⏳ **Phase 4 Batch 4** (in progress): **cart drawer** — currently CART icon links to `/cart` (full page); mockup expects slide-out drawer. Need restyle of Horizon's `<cart-drawer>` + wire `ds-header` trigger.
- ⏳ **Phase 4 Batch 5**: About page (`ds-about` + `templates/page.about.json`).
- ⏳ **Phase 4 Batch 6**: Collection page restyle.
- ⏳ **Phase 5 Polish**: AJAX add-to-cart via Horizon `<product-form-component>` (currently native form submit on PDP — page reloads), self-host Inter Tight + JetBrains Mono (currently `@import` from Google Fonts), container width strategy revisit (full-bleed vs constrained), prune ~100 lines of unused Horizon PDP CSS overrides from `ds-tokens.css` (from early Phase 3 before we went custom), `prefers-reduced-motion` audit, Theme Check + Lighthouse pass.

## Per-product admin setup the merchant still needs

**Metaobject definitions** (admin → Settings → Custom data → Metaobjects):
- `Feature` (label, title, body, images list, captions list) — ✅ **done**
- `FAQ item` (question, answer) — ✅ **done**
- `CTA inset` (eyebrow, heading, body, button_label, button_url, image) — pending
- `Video` (url, file, thumbnail, heading, subhead, meta_text, duration) — pending

**Product metafields** (admin → Settings → Custom data → Products):
- `disrupted.features` (list of Feature) — ✅ **done**
- `disrupted.faq` (list of FAQ item) — ✅ **done**
- `disrupted.ctas` (list of CTA inset) — pending
- `disrupted.video` (single Video reference) — pending
- `disrupted.font_file` (file, accepts .woff2/.ttf/.otf) — pending
- `disrupted.badge_text`, `disrupted.tagline`, `disrupted.demo_url` — pending
- Specs: `disrupted.compat`, `disrupted.format`, `disrupted.file_size`, `disrupted.license`, `disrupted.updates`, `disrupted.support` — pending

All optional. Sections hide gracefully when metafields are blank.

Step-by-step setup in `docs/changes.md`.

## Per-template assignment (per product, in admin)

| Product type | Template |
|---|---|
| BACKLIT, FLUX, future plugins | `product.plugin` |
| Neue Portal, Rivet Mono, future fonts | `product.font` |
| Chroma Vol. 01, Botanic Library, graphics, misc | `product` (default) |

Set via product admin → right sidebar → "Theme template" dropdown.

## Menus the merchant needs to set up in admin (Online Store → Navigation)

- **Main menu**: Shop / Plugins / Fonts / Graphics / About (replaces Horizon's Home/Catalog/Contact)
- **Footer menu**: Plugins / Fonts / Graphics / Free assets / Bundles
- **Studio menu**: About / Contact / License / FAQ / Refunds (optional, second footer column)

## Git history
```
34c70f8  phase 4 batches 1-3: custom header + custom footer
5aca501  phase 3 batch 4: per-product-type templates + 4 new PDP sections
20d3dd7  phase 3 batches 2+3: PDP features + FAQ (metaobject-driven)
dd94a91  phase 3 batch 1 v2: full custom ds-product-main section
57e32a9  phase 3 batch 1 + polish (deprecated by dd94a91 — CSS still present, prune in Phase 5)
d4ae7b7  phase 2 batch 4: ds-wordmark
2fd7f39  phase 2 batch 3: category teasers + newsletter
ebca7e7  phase 2 batch 2: product card + grid + clean index.json
aab8b5d  phase 2 batch 1: hero + trust marquee
caf3173  phase 1: brutalist settings
e3b0852  phase 1: tokens
29854dc  .gitignore
```

## Known small things to revisit (Phase 5)
- Git committer auto-derived as `High Country eComm <highcountryecomm@Highs-MacBook-Pro.local>` — never set proper repo-local `user.name`/`user.email`. Cosmetic only.
- Captions field on Feature metaobject was created as **One** instead of **List** in admin. Either fix the field type in admin or live without paired captions per image.
- ~100 lines of unused Horizon PDP CSS in `ds-tokens.css` (from early Phase 3 before we went custom) — prune.
- CTAs interleaved between feature strips on BACKLIT mockup (Feature 1 → CTA 1 → Feature 2 → Feature 3 → CTA 2 → Feature 4) — our architecture renders all features then all CTAs. Would need polymorphic "PDP block" metaobject to do true interleaving. Deferred.
- Storefront password (`iadren`) in `.env` — fine for dev, will be removed when deploying to real store (where the storefront isn't password-protected).
