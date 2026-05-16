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

## 2026-05-16 — Phase 3 Batch 1 v2: full custom PDP section

After iterating on Horizon's `product-information` block scaffolding,
decided to **replace it entirely** with a custom section. The block-by-
block patching approach kept fighting the platform — Horizon's PDP is
composed of dozens of nested generic blocks with auto-generated class
suffixes, making targeted CSS overrides brittle and the gap to the
mockup hard to close.

### Custom adds
- `sections/ds-product-main.liquid` — Full custom PDP section. Renders:
  - Breadcrumb (auto-derived from product's first non-trivial collection)
  - 7:5 grid: gallery left, info column right (sticky on desktop)
  - Gallery: main image + up to 8 thumbnails below, vanilla JS thumb-swap
  - Eyebrow row: `product.type` + optional orange NEW badge (tag-triggered)
  - Title (clamp 36–72px, semibold, tight)
  - Tagline (from metafield)
  - Inline price row: price + compare-at strike + auto LAUNCH −X% tag
  - ATC button (no quantity selector — single-add only)
  - Variant `<select>` for multi-variant products; hidden for single
  - Sub-CTA "Watch the demo →" (from metafield)
  - Specs table (6 metafield rows; hides if all blank)
  - Long description (`product.description`)

### Vendor edits
- `templates/product.json` — **Fully replaced**. Stock Horizon PDP
  composition (400+ lines, ~30 nested blocks) → minimal 20-line template
  referencing our custom section. All previous Batch 1 wiring (ds-eyebrow,
  ds-tagline, ds-sub-cta, ds-sale-tag, gallery settings) becomes obsolete
  because the custom section owns all of that internally.

### Deferred CSS cleanup
- `assets/ds-tokens.css` — ~100 lines of Horizon PDP overrides
  (.product-information, .product-details, .price, .compare-at-price,
  .add-to-cart-button, .quantity-selector, .sticky-add-to-cart) are now
  unused because we don't render Horizon's PDP anymore. Leaving them
  in place for now — they don't conflict and they'll be useful if we
  ever revert. Phase 5 polish can prune.

### Metafield setup required for full per-product content

The custom section reads optional metafields from the `disrupted`
namespace. Without these, the section still renders (just hides the
optional rows). To enable per-product editing of tagline, specs,
version, and demo URL, set up metafield definitions in admin:

**Settings → Custom data → Products → Add definition**

For each of the following, choose **Single line text** (or **URL** for
demo_url) with namespace `disrupted`:

| Key         | Type         | Example value                                  |
|-------------|--------------|------------------------------------------------|
| `tagline`   | Single line  | "Real backlight glow for Photoshop..."         |
| `version`   | Single line  | "v1.0"                                         |
| `demo_url`  | URL          | "https://youtube.com/watch?v=..."              |
| `compat`    | Single line  | "Photoshop 2022+"                              |
| `format`    | Single line  | "UXP / .ccx"                                   |
| `file_size` | Single line  | "12.4 MB"                                      |
| `license`   | Single line  | "Personal & commercial"                        |
| `updates`   | Single line  | "Free, lifetime"                               |
| `support`   | Single line  | "Direct, by email"                             |

Once defined, each product's edit page in admin shows a "Metafields"
section where you fill these in per-product.

### NEW badge logic

Auto-renders when product has tag "new" / "New" / "NEW". Reads
`product.metafields.disrupted.version` for the version suffix
(defaults to "v1.0" if metafield is empty).

### What's still deferred to next batches

- **Phase 3 Batch 4**: `templates/product.backlit.json` — optional alt
  template if a specific product needs a wildly unique layout.
- **Phase 5 polish**: upgrade the form to use Horizon's
  `<product-form-component>` for AJAX add-to-cart + cart-drawer
  integration. Currently the form submits natively (page reload).

---

## 2026-05-16 — Phase 3 Batch 2 & 3: features + FAQ via metaobjects

Built the two remaining PDP body sections. Both read content from
**Shopify metaobjects** referenced via product list-metafields. This
gives per-product editing in a clean admin UI: each product can have
totally different features and FAQ items, all managed from the product
admin page (no theme editor visits, no template juggling).

### Custom adds
- `sections/ds-pdp-features.liquid` — Iterates `product.metafields.disrupted.features`
  (list of `feature` metaobjects). Each feature renders a full-width
  strip: head (label + big title + body) + optional 3-col image grid
  with mono captions. Hides entirely if no features set.
- `sections/ds-pdp-faq.liquid` — Iterates `product.metafields.disrupted.faq`
  (list of `faq_item` metaobjects). Renders a native `<details>` accordion
  — no JavaScript needed for open/close. CSS handles the toggle rotate.
  Section heading is configurable (defaults to "Questions").

### Vendor edits
- `templates/product.json` — Added `features` and `faq` sections after
  `main`. Order: main → features → faq.

### Metaobject setup walkthrough

The two sections render NOTHING until you set up the metaobject
definitions + product metafields. Once setup is done, you add features
and FAQ items per product directly from the product admin page.

#### Step 1: Define the "Feature" metaobject

1. **Admin → Settings → Custom data → Metaobjects → Add definition**
2. Name: `Feature`
3. Auto-generated type handle: `feature` (leave as-is)
4. Click **Add field** for each of these:
   - `label` — Single line text — *optional* (e.g. "FEATURE 01"; auto-numbered if blank)
   - `title` — Multi-line text — *optional* (big H2; line breaks render as `<br>`)
   - `body` — Multi-line text — *optional* (supporting paragraph)
   - `images` — File — **List** — *optional* — Accept: Image only (3 thumbnails per strip)
   - `captions` — Single line text — **List** — *optional* (paired with images by order)
5. **Save**

#### Step 2: Define the "FAQ item" metaobject

1. **Admin → Settings → Custom data → Metaobjects → Add definition**
2. Name: `FAQ item`
3. Auto-generated type handle: `faq_item`
4. Add fields:
   - `question` — Single line text — *required*
   - `answer` — Multi-line text — *required*
5. **Save**

#### Step 3: Add the product metafield definitions

1. **Admin → Settings → Custom data → Products → Add definition**
2. **First definition**:
   - Name: `Features`
   - Namespace and key: `disrupted.features`
   - Type: **Metaobject** → **List of entries** → pick `Feature`
3. **Save**
4. **Add definition** again:
   - Name: `FAQ`
   - Namespace and key: `disrupted.faq`
   - Type: **Metaobject** → **List of entries** → pick `FAQ item`
5. **Save**

#### Step 4: Add content to a product

1. **Admin → Products → click any product**
2. Scroll past the description — you'll see the **Metafields** section
3. Click `Features` → **Add Feature** → fill in label/title/body, upload images, add captions → **Save**
4. Add up to 3 features per strip; multiple strips per product if needed
5. Click `FAQ` → **Add FAQ item** → fill in question/answer → **Save**
6. Repeat for each Q&A

Refresh the PDP — features strips and FAQ accordion appear in the order you set.

### What's still deferred

- **Phase 5 polish**: AJAX add-to-cart via Horizon's `<product-form-component>`.

---

## 2026-05-16 — Phase 3 Batch 4: per-product-type templates + 4 new PDP sections

Expanded the PDP architecture to support genuinely different layouts per
product type while keeping content per-product editable. Plugin products
(BACKLIT, FLUX) get an elaborate layout with multiple feature strips,
promotional CTAs, and a video. Font products get a live type specimen
that loads the actual font file. Graphics + other products use the default.

### Custom adds — 4 new sections
- `sections/ds-pdp-cta-inset.liquid` — Metaobject-driven CTA banner. Reads
  `product.metafields.disrupted.ctas` (list of `cta_inset` metaobjects).
  Multiple CTAs stack vertically with alternating image side (left/right).
  Each card: eyebrow + heading + body + button + image.
- `sections/ds-pdp-video.liquid` — Metaobject-driven video embed. Reads
  `product.metafields.disrupted.video` (single `video` metaobject ref).
  Priority: uploaded file > YouTube URL > Vimeo URL > static thumbnail
  with decorative play overlay > section hides. Includes mono meta-text
  + duration labels per mockup.
- `sections/ds-pdp-related.liquid` — Related products grid. Pulls from
  the product's first non-trivial collection (not Shopify's ML
  recommendations API — simpler, server-rendered, no async fetch).
  Uses our `ds-product-card` snippet for visual consistency with the
  homepage Featured Work section. Excludes the current product.
- `sections/ds-pdp-typespecimen.liquid` — Live type specimen for font
  products. Reads `product.metafields.disrupted.font_file` (any of
  .ttf/.otf/.woff/.woff2). Inlines `@font-face` with the file URL, then
  renders a huge display quote + (optional) alphabet/numerals/symbols
  rows in the actual loaded font. Hides if no font file uploaded.

### Custom adds — 2 new templates
- `templates/product.plugin.json` — Order: main → features → cta → video →
  faq → related. For BACKLIT, FLUX, etc.
- `templates/product.font.json` — Order: main → typespecimen → faq →
  related. For Neue Portal, Rivet Mono, etc.

### Vendor edits
- `templates/product.json` — Default template now includes `related` as
  the 4th section. Order: main → features → faq → related.

### Per-product template assignment

In admin → Products → [pick product] → look in the right sidebar for
**"Theme template"** dropdown:
- **BACKLIT, FLUX, future plugins**: choose `product.plugin`
- **Neue Portal, Rivet Mono, future fonts**: choose `product.font`
- **Chroma Vol. 01, Botanic Library, etc.**: leave as `product` (default)

The dropdown only lists templates that exist in `/templates/`, so the
new ones are picked up automatically.

---

### New metaobject + metafield setup walkthrough (Phase 3 Batch 4)

You've already set up `Feature`, `FAQ item`, `disrupted.features`,
`disrupted.faq` (Batch 3). Now adding 4 more for the new sections.

#### Step 1: Define the "CTA inset" metaobject

1. **Admin → Settings → Custom data → Metaobjects → Add definition**
2. Name: `CTA inset`
3. Auto-handle: `cta_inset`
4. Add fields (all *optional*):
   - `eyebrow` — Single line text
   - `heading` — Multi-line text *(line breaks render as `<br>`)*
   - `body` — Multi-line text
   - `button_label` — Single line text
   - `button_url` — URL
   - `image` — File — Single (NOT list) — Accept: **Image only**
5. Save

#### Step 2: Define the "Video" metaobject

1. **Add definition** again
2. Name: `Video`
3. Auto-handle: `video`
4. Add fields (all *optional*):
   - `url` — URL *(YouTube or Vimeo)*
   - `file` — File — Single — Accept: **Video only**
   - `thumbnail` — File — Single — Accept: **Image only** *(used as poster)*
   - `heading` — Single line text *(section title)*
   - `subhead` — Multi-line text *(section subhead)*
   - `meta_text` — Single line text *(top-left mono label, e.g. "DEMO / BACKLIT V1.0")*
   - `duration` — Single line text *(e.g. "90s" or "01:34")*
5. Save

#### Step 3: Define the 3 new Product metafields

In **Admin → Settings → Custom data → Products → Add definition** for each:

**Definition: `disrupted.ctas`**
- Name: `CTAs`
- Namespace and key: `disrupted.ctas`
- Type: **Metaobject → List of entries → CTA inset**
- Save

**Definition: `disrupted.video`**
- Name: `Video`
- Namespace and key: `disrupted.video`
- Type: **Metaobject → One entry → Video** *(One, NOT List — one video per product)*
- Save

**Definition: `disrupted.font_file`**
- Name: `Font file`
- Namespace and key: `disrupted.font_file`
- Type: **File → One file**
- Under "Accepted file types" pick **Other files** (since fonts aren't categorised as image/video). Or leave "Any file" — Shopify allows .woff2/.ttf/.otf uploads.
- Save

#### Step 4: Add content per product

For a plugin product like BACKLIT (once you add it):
1. Product admin → Theme template dropdown → pick `product.plugin`
2. Scroll to Metafields:
   - **Features** → Add 3-4 Feature entries
   - **CTAs** → Add 1-2 CTA entries
   - **Video** → Add a Video entry (set URL or file, plus heading/subhead/meta/duration)
   - **FAQ** → Add 5+ FAQ items
3. Save product

For a font product like Neue Portal:
1. Product admin → Theme template → `product.font`
2. Upload the .woff2 to `disrupted.font_file` metafield
3. Add FAQ items
4. Save product

For graphics products: just use the default template — fill in Features + FAQ.

### Notes & limitations

- **CTA inset interleaving**: The mockup has CTAs *between* feature strips
  (Feature 1 → CTA 1 → Feature 2 → Feature 3 → CTA 2 → Feature 4). Our
  current architecture puts all features in one section and all CTAs in
  one section after them. To achieve true interleaving we'd need a more
  complex "PDP block" polymorphic metaobject. Deferred to Phase 5 unless
  it becomes critical.
- **Font file CSP**: Shopify CDN serves font files cross-origin friendly,
  but if the typespecimen doesn't load, check the browser console — some
  ad blockers or custom CSPs may block `@font-face` URLs.
- **Related products**: uses collection-based fallback (simpler than the
  recommendations API which requires async fetch). Each product's first
  non-"all"/"frontpage" collection is the source. If you want ML-based
  recommendations, that's a Phase 5 upgrade.

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
