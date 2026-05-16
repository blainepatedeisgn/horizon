# Disrupted Studio — Customisation Log

Every change to Horizon vendor files, every custom file added, every theme-setting overridden — logged here with the date and reason. When upgrading Horizon (`git merge upstream/main`), this is the file that tells us what to re-test.

## Conventions
- **Vendor edit** — change to a file Horizon ships. Highest scrutiny. Re-check on every upstream pull.
- **Custom add** — new `ds-*` prefixed file under our control. Safe by design; will not collide with upstream.
- **Setting override** — key in `config/settings_data.json` changed from Horizon's default.

---

## 2026-05-16 — Phase 1 foundations

### Custom adds
- `docs/changes.md` — This log.
- `assets/ds-tokens.css` — Design tokens (CSS variables, base reset, utility classes). Source: handoff doc §5 + mockup lines 15–122.

### Vendor edits
- `layout/theme.liquid` — Added one line between line 38 (`{{ content_for_header }}`) and line 39 (`</head>`):
  ```liquid
  {{ 'ds-tokens.css' | asset_url | stylesheet_tag }}
  ```
  **Why**: Loads ds-tokens.css *after* Horizon's own CSS (rendered earlier via `stylesheets`, `theme-styles-variables`, `color-schemes`), so our overrides win the cascade without `!important`. Per handoff doc §5 this is the only acceptable edit to `theme.liquid`.

### Setting overrides (`config/settings_data.json`)

**Typography** — Inter Tight + JetBrains Mono are loaded via `@font-face` from Google Fonts in `ds-tokens.css` (the handoff spec §5 claimed Shopify's font library has these handles but it does NOT — `inter_tight_n4` was rejected with `'inter_tight_n4' is not a valid font handle`). Shopify font settings kept on `inter_n*` as a sensible fallback chain:
- `type_body_font`: `inter_n4` (unchanged from Horizon default)
- `type_subheading_font`: `inter_n5` (unchanged)
- `type_heading_font`: `inter_n7` → `inter_n6` (semibold, less heavy — matches mockup heading weight)
- `type_accent_font`: `inter_n7` → `inter_n5` (Inter at medium; real accent font is JetBrains Mono via `--font-mono` token in `ds-tokens.css`)

**Color schemes**:
- `scheme-1` → Studio Dark (`#000000` bg, `#F2F0EC` fg, light buttons with dark text)
- `scheme-2` → Studio Dark Elevated (`#0A0A0A` bg, otherwise identical to scheme-1)
- `scheme-3` → Accent (`#FF4D1F` orange bg, `#0A0A0A` fg)
- `scheme-4`–`scheme-6` left untouched (merchant-editable extras)

**Badge scheme references**:
- `badge_sale_color_scheme`: `scheme-1` → `scheme-3` (sale badge = orange accent)
- `badge_sold_out_color_scheme`: `scheme-3` → `scheme-2` (sold-out = subtle elevated dark)

**Corner radii** (all → `0` for brutalist squared aesthetic):
- `badge_corner_radius`: `100` → `0`
- `button_border_radius_primary`: `14` → `0`
- `button_border_radius_secondary`: `14` → `0`
- `inputs_border_radius`: `4` → `0`
- `popover_border_radius`: `14` → `0`
- `card_corner_radius`: `4` → `0`
- `variant_swatch_radius`: `32` → `0`
- `variant_button_radius`: `14` → `0`
- `product_corner_radius`: already `0`, unchanged

**Layout**:
- `page_width`: `narrow` → `wide` (mockup uses 1600px max width)

### Additional custom-add deviation from spec
- `assets/ds-tokens.css` — Added `@import url('https://fonts.googleapis.com/css2?family=Inter+Tight:...&family=JetBrains+Mono:...&display=swap')` at the top. Per handoff doc this should not be needed (it claimed Shopify has these fonts), but since Shopify's font library does NOT include Inter Tight or JetBrains Mono, we load via Google Fonts. One extra HTTP request as a result. Could be self-hosted in Phase 5 polish to eliminate the external request.

---

## 2026-05-16 — Phase 2 Batch 1: hero + trust marquee

### Custom adds
- `blocks/ds-eyebrow.liquid` — Small caps label with optional inline mono accent.
- `blocks/ds-hero-title.liquid` — Display H1 with blurred white glow halo. Uses `escape | newline_to_br` to allow merchant-authored line breaks.
- `blocks/ds-meta-strip.liquid` — 3 items separated by `/`, optional pulsing accent dot on the last item.
- `blocks/ds-rich-text.liquid` — Brand-styled rich-text paragraph (caps at ~38ch).
- `blocks/ds-button-row.liquid` — Primary button + secondary text-link with arrow.
- `snippets/ds-backlit-render-fallback.liquid` — CSS-art glowing "B" placeholder for the hero visual when no image is uploaded. Self-contained with inline `{% style %}`.
- `sections/ds-hero.liquid` — Two-column hero. Left = `{% content_for 'blocks' %}` (renders all hero blocks in order); right = image_picker or BACKLIT fallback.
- `sections/ds-trust-marquee.liquid` — Scrolling marquee with section blocks (inline schema). Logos rendered twice via Liquid `(1..2)` loop for seamless `transform: translateX(-50%)` keyframe loop. Pauses on hover; disables with `prefers-reduced-motion`.

### Deviations from handoff spec §7a
The handoff doc shows `{% content_for 'blocks', type: 'X' %}` for per-type block routing into grid slots. **That syntax does not exist in Shopify Liquid.** Verified against actual Horizon sections (`sections/collection-list.liquid`, `sections/header.liquid`): only `{% content_for 'blocks' %}` (all blocks) and `{% content_for 'block', type: 'X', id: 'Y' %}` (single static block by ID) are valid. So:
- **Hero**: Collapsed to two columns (content stack left, visual right). All content blocks render in their schema order via single `{% content_for 'blocks' %}`. Merchant controls order via editor reorder.
- **Trust marquee**: Used SECTION blocks (inline definition in section schema) instead of theme blocks, because we need to iterate twice in Liquid for the seamless-loop trick.

### Vendor edits
- `assets/ds-tokens.css` — appended ~110 lines of component primitives: `.ds-btn` (variants: `--full`, `--lg`, `--secondary`, `--accent`), `.ds-button-row`, `.ds-meta-strip` (with pulsing dot), `.ds-rich-text`. Block-level shared styles only; section-specific CSS stays inline in `{% style %}`.

### Setting overrides
_(none — Phase 2 Batch 1 introduces no settings_data.json changes)_

---

## 2026-05-16 — Phase 2 Batch 2: product card + grid + clean index.json

### Custom adds
- `snippets/ds-product-card.liquid` — Self-contained product card. Renders primary + optional secondary (hover-swap) images, auto-detects SALE/NEW badges (compare-at-price + tag-based), and shows title + category subline + mono price with strike-through compare-at.
- `sections/ds-product-grid.liquid` — Featured products section. 3-col grid (2 on tablet, 1 on phone). Section heading uses brutalist `.ds-section-head` pattern (H2 with mono `num` superscript + subhead). Settings: color scheme, heading + num + subhead text, collection picker, products_to_show range (2–12), view-all link config.

### Vendor edits
- `assets/ds-tokens.css` — Appended ~90 lines of product-card CSS (`.ds-product-card`, `.ds-product-card__media`, hover-layer fade/scale, badge variants, meta + title-block + price + strike).
- `templates/index.json` — **Fully replaced**. Stock Horizon homepage (288 lines of stock hero + multicolumn + image-with-text + etc.) replaced with a clean 3-section layout: ds-hero, ds-trust-marquee, ds-product-grid. All blocks pre-wired (no editor manipulation needed to see the brutalist homepage). Originally planned for Phase 2 Batch 4 but pulled forward so the dev preview at 127.0.0.1:9292 shows only our custom work.

### Deviation from mockup
- `.ds-product-grid-section__inner`: dropped the `max-width: var(--container-max); margin: 0 auto` constraint. Mockup wraps `.section` content in a 1600px `.container` for centering on wide viewports, but the user requested full-bleed alignment with the hero/marquee. Now uses only `padding-left/right: var(--pad-x)` matching hero. To revisit in Phase 5 polish: consider a unified container strategy.

### Setting overrides
_(none — Phase 2 Batch 2 introduces no settings_data.json changes)_

---

## 2026-05-16 — Phase 2 Batch 3: category teasers + newsletter card

### Custom adds
- `sections/ds-category-teasers.liquid` — Baseline-aligned vertical category rows: mono "/ NN" forloop index, big display title (clamp 36–76px), mono meta lines on the right, hover-nudged arrow. Title turns accent orange on hover; arrow translates up-right. Tiles are section blocks (inline schema) since they're category-teaser-specific and need forloop access for auto numbering. Meta lines auto-fall-back to `{products_count} products` and `FROM {price_min}` if left blank and a collection is set.
- `sections/ds-newsletter-card.liquid` — Two-column bordered card on desktop (copy left, form right), stacks on <=800px. Eyebrow + multi-line title + sub. Uses `{% form 'customer' %}` for native Shopify customer subscription — no JavaScript. Optional `signup_tag` setting adds a tag to created customer records for segmentation. Success and error messages handled inline via `form.posted_successfully?` and `form.errors`.

### Vendor edits
- `templates/index.json` — Added two sections to homepage order: `category_teasers` (preset with Plugins / Fonts / Graphics tiles) and `newsletter`. Now 5 sections in total.

### Setting overrides
_(none — Phase 2 Batch 3 introduces no settings_data.json changes)_

---

## 2026-05-16 — Phase 2 Batch 4: wordmark + Phase 2 complete

### Custom adds
- `sections/ds-wordmark.liquid` — Giant viewport-wide bold display wordmark with optional mono ® superscript. Single-line, centered, unselectable, overflow:hidden. clamp(56px, 18vw, 256px) at desktop; clamp(56px, 19vw, 200px) on phones with looser letter-spacing.

### Vendor edits
- `templates/index.json` — Added `wordmark` as the 6th and final section in the homepage order. Phase 2 homepage now complete: hero → trust marquee → product grid → category teasers → newsletter → wordmark.

### Note on placement
The handoff doc §6 specifies the wordmark goes "below the footer in index.json". Shopify renders template sections inside `<main>`, with footer-group rendered after via `theme.liquid`, so the wordmark literally cannot sit below the footer without either moving it into footer-group or editing theme.liquid. For now it sits as the last main-content section, above Horizon's footer. Phase 5 may revisit if we want it visually after the footer (would require footer-group integration).

### Setting overrides
_(none — Phase 2 Batch 4 introduces no settings_data.json changes)_

---

## Phase 2 summary

All six brutalist homepage sections built + wired:
- Hero (5 theme blocks)
- Trust marquee (12 section blocks for logos)
- Featured product grid + reusable product card snippet
- Category teasers (3 section blocks for tiles)
- Newsletter card (native `{% form 'customer' %}`)
- Giant DISRUPTED® wordmark

Vendor file touch count: `assets/ds-tokens.css` appended (component primitives + product card), `layout/theme.liquid` 1 line added, `config/settings_data.json` modified (Phase 1 only), `templates/index.json` fully rewritten.

Custom files added: 5 sections, 5 blocks, 2 snippets.
