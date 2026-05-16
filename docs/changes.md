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
