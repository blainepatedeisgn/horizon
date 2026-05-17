# Product Content Spec — Disrupted Studio PDPs

This document tells you **everything you need to gather** to ship a fully-
populated product page on the Disrupted Studio theme — both the BACKLIT
reference build and the template variants used by your other 8 products.

The theme has 4 product templates. Each product gets one assigned via
**Admin → Product → Theme template** (dropdown on the right side of the
product edit page):

| Template handle      | For…                                                | Sections (in render order)                                 |
|----------------------|-----------------------------------------------------|------------------------------------------------------------|
| `product.plugin`     | Photoshop plugins / software (BACKLIT, FLUX, etc.)  | main → features → cta → video → faq → related              |
| `product.font`       | Type / fonts (Neue Portal, Rivet Mono, etc.)        | main → typespecimen → faq → related                        |
| `product.graphics`   | Asset packs (icons, libraries, grain studies)       | main → features → faq → related                            |
| `product.bundle`     | Multi-item bundles                                  | main → bundle_contents → faq → related                     |

All four share the same `ds-product-main` hero. The differences are in the
section stack below the hero.

---

## PART 1 — BACKLIT (plugin template) full content checklist

Use this as the master checklist for BACKLIT. Every field is optional —
sections without content gracefully hide. But the design assumes most of
these are filled in.

### 1.1 — Native Shopify fields (Product edit page, top)

| Field                          | What to enter                                                                  | Example for BACKLIT                                              |
|--------------------------------|--------------------------------------------------------------------------------|------------------------------------------------------------------|
| **Title**                      | The product name as it appears as the H1                                       | `BACKLIT`                                                        |
| **Description**                | Long-form HTML; renders BELOW the spec table                                   | 2–4 short paragraphs, can include `<strong>`, `<ul>`, etc.       |
| **Media** (gallery)            | First image = featured (hero); up to 8 thumbs render under the main           | 1× hero shot + 4–8 detail/screen shots                           |
| **Price**                      | What the buyer pays                                                            | `$48.00`                                                         |
| **Compare-at price**           | Optional. Renders strike-through + auto `LAUNCH −X%` tag if set                | `$60.00`                                                         |
| **Product type**               | Drives the small caps eyebrow above the title                                  | `Photoshop Plugin`                                               |
| **Theme template**             | Choose `product.plugin`                                                        | `product.plugin`                                                 |
| **Collections**                | First non-`all`/`frontpage` collection drives the breadcrumb                   | `Plugins`                                                        |

### 1.2 — Hero metafields (namespace `disrupted`)

Set these per-product in the **Metafields** panel at the bottom of the
product edit page. All optional; missing fields just hide their UI element.

| Metafield key                     | Type                       | What it does                                                                                                       | Example                                                |
|-----------------------------------|----------------------------|--------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| `disrupted.badge_text`            | Single line text           | Orange pill badge next to the eyebrow (e.g. version flag, launch flag). Blank = hidden.                            | `NEW · v1.0`                                           |
| `disrupted.tagline`               | Single line text           | One-line subhead under the H1, sits above the price                                                                | `One-click cinematic backlight for Photoshop`          |
| `disrupted.demo_url`              | URL                        | Destination for the "Watch the demo" link under the ATC. Can be a `#section-id` for in-page scroll or a full URL.  | `#video`                                               |
| `disrupted.sub_cta_label`         | Single line text           | Override the default "Watch the demo" label                                                                        | `Watch the 90-second demo`                             |
| `disrupted.plugin_specs`          | Metaobject (`plugin_specs`)| 6-row spec table next to the buy button — see fields below                                                         | See 1.3                                                |

### 1.3 — Plugin specs metaobject (`plugin_specs`)

One entry per product, attached via the `disrupted.plugin_specs` metafield.
Each row hides if its value is blank. Renders as the bordered spec table
between the sub-CTA and the long description.

| Field        | Example for BACKLIT                                  |
|--------------|------------------------------------------------------|
| `compat`     | `Photoshop 24+ (Mac · Windows)`                      |
| `format`     | `UXP plugin (.ccx)`                                  |
| `file_size`  | `2.4 MB`                                             |
| `license`    | `Single seat · commercial use`                       |
| `updates`    | `Free for life`                                      |
| `support`    | `Email — 48h response`                               |

### 1.4 — Features section (`disrupted.features`, list of `feature` metaobjects)

Each `feature` metaobject entry = one full-width strip with a heading,
body, and up to 3 images. Aim for **2–4 entries** for BACKLIT.

Per-entry fields:

| Field        | Type                       | Notes                                                                | Example                                                       |
|--------------|----------------------------|----------------------------------------------------------------------|---------------------------------------------------------------|
| `label`      | Single line text           | Mono caps eyebrow (e.g. `[ FEATURE 01 ]`). Auto-numbers if blank.    | `[ FEATURE 01 ] · LIGHT ENGINE`                               |
| `title`      | Multi-line text            | Big H2. Line breaks → `<br>`. 2–6 words/line, 1–3 lines total.       | `Real cinematic\nbacklight,\none click.`                      |
| `body`       | Multi-line text            | 1–3 sentence supporting paragraph                                    | `Volumetric god-rays, hue-preserving HDR bloom, film halation, and anamorphic streak — all in pure JS over Float32 buffers.` |
| `images`     | List of file references    | Up to 3 thumbnails per strip. Recommended: 1200×900 PNG/JPG          | Three before/after screenshots                                 |
| `captions`   | List of single line text   | Paired captions for each image (same order)                          | `Before`, `After`, `Slider drag · live`                       |

**BACKLIT feature strip suggestions:**

1. **LIGHT ENGINE** — the pipeline (rays, bloom, rim, halation, streak, fog, dust)
2. **LIVE EDITING** — every slider live-updates the document layer in real time
3. **CHROMATIC CONTROL** — white, teal, sodium, lavender pickers with hue-preserving tonemap
4. **NON-DESTRUCTIVE** — single Screen-blended layer, never touches the original

### 1.5 — CTA insets (`disrupted.ctas`, list of `cta_inset` metaobjects)

Each entry = one promotional card. Layout alternates left/right between
cards. Use 1–2 for BACKLIT — these are launch promos, bundle nudges, or
demo CTAs.

| Field          | Type             | Example                                                       |
|----------------|------------------|---------------------------------------------------------------|
| `eyebrow`      | Single line text | `LAUNCH WEEK · 20% OFF`                                       |
| `heading`      | Multi-line text  | `Try it before\nSunday.`                                      |
| `body`         | Multi-line text  | `Free updates for life, 14-day refund, no subscription.`      |
| `button_label` | Single line text | `Get BACKLIT — $48`                                           |
| `button_url`   | URL              | `#` (scroll to top) or external                               |
| `image`        | File reference   | 1600×1200 ideally; renders large on the left or right         |

### 1.6 — Video section (`disrupted.video`, single `video` metaobject)

One demo video, embedded inline. Priority: uploaded file > YouTube > Vimeo > static thumbnail.

| Field         | Type             | Example                                                  |
|---------------|------------------|----------------------------------------------------------|
| `url`         | URL              | `https://www.youtube.com/watch?v=...`                    |
| `file`        | File / Video     | OR upload an `.mp4` directly                             |
| `thumbnail`   | File reference   | Poster image (1600×900); also fallback if no video       |
| `heading`     | Single line text | `See it in action`                                       |
| `subhead`     | Multi-line text  | `30 seconds. White background. No talking.`              |
| `meta_text`   | Single line text | Mono label top-left of the player frame                  |
| `duration`    | Single line text | `90s` — renders as the section number                    |

### 1.7 — FAQ (`disrupted.faq`, list of `faq_item` metaobjects)

Accordion of common questions. Aim for **5–8 entries** for BACKLIT.

| Field      | Type                | Example                                                                                       |
|------------|---------------------|-----------------------------------------------------------------------------------------------|
| `question` | Single line text    | `Does this work on Photoshop CS6?`                                                            |
| `answer`   | Multi-line text     | `No — UXP plugins require Photoshop 24 (2023) or later on Mac and Windows. CC 2024+ recommended.` |

**Suggested BACKLIT FAQ topics:**

- Photoshop version compatibility
- Installing the `.ccx` file
- Difference between BACKLIT and Photoshop's built-in Outer Glow
- Whether it works on raster, text, smart objects, etc.
- License — single seat / team / commercial usage rights
- Updates — what version cadence to expect
- Refund policy
- Where to get support

### 1.8 — Related products (automatic)

Pulls from `product.collections.first`. Just make sure BACKLIT is in a
collection that contains the products you want to surface (e.g. a
`Plugins` collection).

---

## PART 2 — Graphics needed for BACKLIT

Group all graphics in a single shared folder. Naming convention:
`backlit-<role>-<index>.<ext>`.

| Asset                              | Dimensions (px)    | Format          | Quantity  | Notes                                                                            |
|------------------------------------|--------------------|-----------------|-----------|----------------------------------------------------------------------------------|
| Hero / featured image              | 2000×2000 (1:1)    | PNG or JPG      | 1         | First in product media. Gallery is square (`aspect-ratio: 1/1`).                |
| Gallery thumbs (additional images) | 2000×2000 (1:1)    | PNG or JPG      | 3–7       | Detail shots, before/after pairs, plugin UI screenshots                          |
| Feature strip images               | 1200×900 (4:3)     | PNG or JPG      | 6–12      | 3 per feature strip × 2–4 strips                                                 |
| CTA inset visual                   | 1600×1200          | PNG or JPG      | 1–2       | One per CTA card                                                                 |
| Video poster (thumbnail)           | 1600×900 (16:9)    | JPG             | 1         | Fallback frame for the video block                                               |
| Demo video                         | 1920×1080 (16:9)   | MP4 (H.264)     | 1         | Under 25 MB if uploading direct to Shopify; otherwise YouTube/Vimeo URL          |

**Color/style guidance:** the theme is brutalist on a dark scheme. Hero
shots that already look cinematic (dark background, bright subject,
visible backlight effect) outperform clean white-background product
shots on this template.

---

## PART 3 — Other template variants summary

For your remaining 8 products, decide which template each uses and gather
the corresponding content. Template-specific differences:

### 3.1 — `product.font` (type products)

**Sections:** main → typespecimen → faq → related

In addition to the hero metafields (sections 1.1–1.2):

| Metafield                       | Type                       | Notes                                                  |
|---------------------------------|----------------------------|--------------------------------------------------------|
| `disrupted.font_specs`          | Metaobject (`font_specs`)  | Replaces `plugin_specs`. Fields: family, glyphs, formats, language, license |
| `disrupted.font_file`           | File reference             | `.woff2`, `.ttf`, `.otf`, or `.woff`. Powers the live type specimen below the hero |
| `disrupted.faq` (same as plugin)|                            | Use FAQ for licensing, formats, OpenType features, etc. |

The typespecimen section renders **the actual font you uploaded** at the
top of the page, with size + tracking sliders. The buyer can scrub the
text live. Make sure you have rights to embed the .woff2 publicly.

### 3.2 — `product.graphics` (asset packs)

**Sections:** main → features → faq → related

Same as plugin template minus the CTA insets and video block.

| Metafield                       | Type                          | Notes                                                |
|---------------------------------|-------------------------------|------------------------------------------------------|
| `disrupted.graphics_specs`      | Metaobject (`graphics_specs`) | Fields: items, formats, resolution, license, updates |
| `disrupted.features`            | List of `feature` metaobjects | Use to showcase asset categories / contents          |
| `disrupted.faq`                 | (same as plugin)              |                                                      |

For asset packs you typically don't need a video — the features grid IS
the demo. But you can still set `disrupted.video` if you want; the section
will hide on this template because it's not in the section order.

### 3.3 — `product.bundle` (multi-product bundles)

**Sections:** main → bundle_contents → faq → related

| Metafield                          | Type                       | Notes                                                                  |
|------------------------------------|----------------------------|------------------------------------------------------------------------|
| `disrupted.bundle_contents`        | Product (list)             | Pick the products included in this bundle                              |
| `disrupted.faq`                    | (same as plugin)           |                                                                        |

**Bundle pricing convention:**
- **Price** = what the buyer pays for the bundle
- **Compare-at price** = sum of individual product prices (gives the
  strike-through on the PDP automatically)
- The "What's included" section computes `you_save` and `you_save_pct`
  automatically from those two prices.

Bundle products typically don't need their own features/CTAs/video —
each included product has its own PDP. Hero gallery is still important.

---

## PART 4 — Research prompt for the other 8 products

Copy the block below verbatim into a new chat with Claude, paste your
store URL where indicated, and Claude will produce the equivalent fill-out
sheet for each of your 8 products.

````
I run a Shopify store on the Disrupted Studio theme. Here's my live site:

  STORE URL: <PASTE YOUR SHOPIFY STORE URL HERE>

The theme has 4 product templates with different section stacks:

  product.plugin    main → features → cta → video → faq → related
  product.font      main → typespecimen → faq → related
  product.graphics  main → features → faq → related
  product.bundle    main → bundle-contents → faq → related

I have one already-fully-populated reference product (BACKLIT, a Photoshop
plugin on the product.plugin template). I have 8 other products that need
the same level of population.

For each of my 8 non-BACKLIT products, please:

1. **Visit its live PDP URL.** Identify which template it should use:
   plugin / font / graphics / bundle.

2. **Map its existing content** to the metafield structure below. Note
   what's already on the page vs. what's missing.

3. **Produce a fill-out sheet** with these sections (skip any not
   applicable to its chosen template):

   a) Native Shopify fields
      • Title
      • Product type (drives the eyebrow caps text)
      • Description (2–4 short paragraphs)
      • Price + compare-at price (if any launch discount)
      • Recommended collections (drives the breadcrumb)
      • Theme template (one of plugin/font/graphics/bundle)

   b) Hero metafields (namespace: disrupted)
      • badge_text       — orange pill, e.g. "NEW · v1.0" or blank
      • tagline          — one-line subhead, max ~80 chars
      • demo_url         — anchor (#video) or full URL
      • sub_cta_label    — override default "Watch the demo"

   c) Specs metaobject (PICK ONE based on template)
      • plugin_specs:   compat, format, file_size, license, updates, support
      • font_specs:     family, glyphs, formats, language, license
      • graphics_specs: items, formats, resolution, license, updates

   d) Features list (plugin + graphics templates) — 2–4 entries, each:
      • label            — mono caps eyebrow
      • title            — H2 (line breaks allowed)
      • body             — 1–3 sentence paragraph
      • image roles      — describe what 1–3 image slots should depict
      • captions         — short paired captions per image

   e) CTA insets (plugin template only) — 1–2 entries, each:
      • eyebrow, heading, body, button_label, button_url, image role

   f) Video (plugin template only)
      • url OR uploaded file note
      • heading, subhead, meta_text, duration

   g) FAQ — 5–8 question/answer pairs that address:
      • Compatibility / requirements
      • Installation / setup
      • What's included / formats
      • Licensing (single seat? commercial? team?)
      • Updates policy
      • Refund policy
      • Support channel
      • Anything product-specific

   h) Bundle contents (bundle template only)
      • List of included Shopify products
      • Note: set compare-at price to the SUM of included products

4. **Graphics manifest** — for each product, list every image asset I
   need to produce or source, with dimensions:
      • Hero (2000×2000)
      • Gallery thumbs (2000×2000) × N
      • Feature strip images (1200×900) × N
      • CTA inset visual (1600×1200) × N
      • Video poster (1600×900) and demo video (1920×1080 MP4, <25 MB)
      • Font file (.woff2) for font products
   For each asset, give a one-line content brief (e.g. "before/after pair
   showing the streak slider at 0 vs 100").

5. **Flag anything missing** — content that's not on the current PDP that
   you'd need to create from scratch (e.g. "no demo video exists yet; need
   to shoot one" or "no FAQ exists; suggesting 6 questions below").

Format your output as ONE markdown section per product, with a clear
heading like `## Product 1 — <name> (<template>)`. Use tables for the
structured fields. End with a summary table listing all 8 products and
the count of assets-to-produce per product.

Use the same brutalist, mono-caps, factual tone as my existing site copy.
No marketing fluff. Specs are facts. Features describe behavior.
````

---

## PART 5 — Quick start checklist for a new product

For when you sit down to populate a single product, here's the workflow:

- [ ] Decide the template (plugin / font / graphics / bundle) — set in product edit page
- [ ] Write title + product type + tagline
- [ ] Set price (+ compare-at if launching at a discount)
- [ ] Shoot or source the hero image (2000×2000)
- [ ] Shoot 3–7 gallery thumbs
- [ ] Write 2–4 paragraphs for the long description
- [ ] Fill in the specs metaobject (plugin / font / graphics)
- [ ] Write 2–4 feature strips (skip for bundle)
- [ ] Source 6–12 feature strip images (1200×900)
- [ ] Write 1–2 CTA insets (plugin only)
- [ ] Source 1–2 CTA inset visuals (1600×1200)
- [ ] Record / source the demo video + write its heading/subhead (plugin only)
- [ ] Upload the font file (.woff2) if it's a font product
- [ ] Pick bundle contents if it's a bundle product
- [ ] Write 5–8 FAQ entries
- [ ] Add the product to the correct collection so the breadcrumb works
- [ ] Set the breadcrumb collection in admin if you want to override
- [ ] Preview the PDP, confirm every section either renders content or hides cleanly

If a section hides unexpectedly, it's almost always because the
corresponding metafield is unset or has a typo. Open the product, scroll
to Metafields, double-check the value is actually saved.
