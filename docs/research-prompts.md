# Product Research Prompts

Copy-paste prompts for asking Claude (or any AI assistant with web access)
to gather product info from your existing Studio 2am vendor storefront
(Blaine Pate Studio) and produce content tailored to the Disrupted Studio
theme's section structure.

## Before you use these

Fill in the two placeholders that appear in every prompt:

- `<SOURCE STORE URL>` — the URL where your products currently live.
  Examples:
    - `https://studio2am.co/collections/blaine-pate-studio` (Studio 2am vendor collection)
    - `https://blainepatestudio.com` (your own brand site, if separate)
    - `https://www.studio2am.co/products/<product-handle>` (specific product page)
- `<PRODUCT NAME>` — e.g. `TXT-84`, `Obscura Gothic`, `BACKLIT`, etc.

Each prompt produces a **fill-out sheet** structured to match this theme's
metafield + section settings, so the output drops cleanly into Shopify
admin without translation.

The FAQ section is now driven by **template blocks** (not metafields), so
the prompts don't ask for per-product FAQs — that content is shared
across all products on the same template and edited in the theme editor.

---

## Prompt 1 — PLUGIN products

Use for: BACKLIT, Pixel Perfect, FLUX, and any future Photoshop plugin.

````
I'm migrating product content from <SOURCE STORE URL> into a new
Shopify storefront using a custom theme. The destination theme uses
4 product templates (plugin / font / graphics / bundle), each with a
specific section stack. I need a fill-out sheet I can paste into
Shopify admin for ONE specific product.

PRODUCT: <PRODUCT NAME>
SOURCE PDP: <SOURCE STORE URL>/products/<product-handle>

Visit the source PDP, read everything (description, hero image alt
text, specs, any embedded video/demo), then produce a fill-out sheet
with these fields. Use the brutalist, factual, terse tone of the
destination site (short declarative sentences, no marketing fluff,
specs are facts).

The destination is a PLUGIN-template product. Section order on this
template is:

  main → features → cta → video → faq → related

The FAQ section is template-shared (not per-product), so SKIP FAQ
research — every plugin product gets the same 6 default questions.

Output sections (skip any not applicable):

1. NATIVE SHOPIFY FIELDS
   • Title
   • Product type (drives the eyebrow caps text — e.g. "Photoshop Plugin")
   • Description (2–4 short paragraphs of HTML — first sentence is a
     one-line tagline like "Live pixel art for Photoshop.")
   • Price (and compare-at price if there's a launch discount)
   • Collections (drives the breadcrumb — e.g. "Plugins")
   • Theme template: product.plugin

2. HERO METAFIELDS (namespace: disrupted)
   • badge_text — orange pill, e.g. "NEW · v1.0" or blank
   • tagline — one-line subhead under the title, ~80 chars max

3. PLUGIN SPECS METAOBJECT (disrupted.plugin_specs)
   Fields, with example values:
   • compat: "Photoshop 23.3+ (Mac · Windows)"
   • format: "UXP plugin (.ccx)"
   • file_size: e.g. "2.4 MB"
   • license: "Personal · Commercial"
   • updates: "Lifetime within v1"
   • support: "Email — 48h response"

4. FEATURES (disrupted.features — list of feature metaobjects, 2–4 entries)
   Each entry:
   • label — mono caps eyebrow like "[ FEATURE 01 ] · LIGHT ENGINE"
   • title — H2, 1–3 lines, line breaks allowed
   • body — 1–3 sentence paragraph
   • image briefs — 1–3 images per strip, describe what each should depict
   • captions — short pair per image

5. CTA INSETS (disrupted.ctas — list of cta_inset metaobjects, 1–2 entries)
   Each entry:
   • eyebrow (e.g. "LAUNCH WEEK · 20% OFF")
   • heading
   • body
   • button_label
   • button_url (anchor or absolute)
   • image brief

6. VIDEO (disrupted.video — single video metaobject, if a demo exists)
   • url OR uploaded file note
   • heading, subhead, meta_text, duration

7. GRAPHICS MANIFEST
   List every image asset you need to source/produce with dimensions:
   • Hero (2000×2000, PNG/JPG)
   • Gallery thumbs (2000×2000) × N
   • Feature strip images (1200×900) × N
   • CTA inset visual (1600×1200) × N
   • Video poster (1600×900) and demo video (1920×1080 MP4)
   For each, give a one-line content brief.

8. GAPS — flag anything missing on the source PDP that needs to be
   created from scratch.

Format as ONE markdown document with clear H2/H3 headings. Tables for
structured fields. Brutalist tone — short sentences, no fluff, no
emojis.
````

---

## Prompt 2 — FONT products

Use for: TXT-84, Obscura Gothic, Neue Portal, Rivet Mono, and any
future typeface.

````
I'm migrating product content from <SOURCE STORE URL> into a new
Shopify storefront using a custom theme. I need a fill-out sheet
for ONE specific font product.

PRODUCT: <PRODUCT NAME>
SOURCE PDP: <SOURCE STORE URL>/products/<product-handle>

Visit the source PDP, read everything, then produce a fill-out sheet.
Use the brutalist, factual, terse tone of the destination site.

The destination is a FONT-template product. Section order:

  main → features → typespecimen → faq → related

The FAQ section is template-shared — SKIP FAQ research.

Output sections:

1. NATIVE SHOPIFY FIELDS
   • Title
   • Product type — e.g. "Display Type", "Mono Sans", "Variable Italic"
   • Description (2–4 short paragraphs)
   • Price + compare-at
   • Collections (e.g. "Type")
   • Theme template: product.font

2. HERO METAFIELDS (namespace: disrupted)
   • badge_text — e.g. "NEW · 6 weights" or blank
   • tagline — one-line subhead

3. FONT SPECS METAOBJECT (disrupted.font_specs)
   • family: e.g. "1 weight · 0 italic" or "6 weights · 1 italic"
   • glyphs: e.g. "412 per weight"
   • formats: "OTF / TTF / WOFF2"
   • language: e.g. "Latin Extended"
   • license: "Personal · Commercial"

4. FONT FILE (disrupted.font_file)
   • .woff2 strongly preferred for the live specimen
   • Note if you have rights to embed the .woff2 publicly

5. FEATURES (disrupted.features — list of feature metaobjects, 1–3 entries)
   Same structure as plugin template. Suggested topics for a font:
   • Anatomy (letterforms, numerals, glyph set)
   • Use cases (display, editorial, identity)
   • Variations (weights, italics, alternates)

6. GRAPHICS MANIFEST
   • Hero (2000×2000) — typically a big letterform or composed wordmark
   • Gallery thumbs (2000×2000) × 3–7 — anatomy details, set examples,
     spec sheet, paragraph samples
   • Feature strip images (1200×900) × N
   • Font file (.woff2 minimum, .otf + .ttf for completeness)

7. GAPS — what's missing on the source PDP?

Format as one markdown doc, brutalist tone, tables for structured
fields, no fluff.
````

---

## Prompt 3 — GRAPHICS products

Use for: Stargazer Graphics Pack, Chroma Vol. 01, Botanic Library,
Arcade Icons, Grain Studies, Music Video Title Templates, etc.

````
I'm migrating product content from <SOURCE STORE URL> into a new
Shopify storefront. I need a fill-out sheet for ONE graphics product.

PRODUCT: <PRODUCT NAME>
SOURCE PDP: <SOURCE STORE URL>/products/<product-handle>

The destination is a GRAPHICS-template product. Section order:

  main → features → faq → related

The FAQ section is template-shared — SKIP FAQ research.

Output sections:

1. NATIVE SHOPIFY FIELDS
   • Title
   • Product type — e.g. "Vector Pack", "Texture Library",
     "Mockup Set", "Title Templates"
   • Description (2–4 short paragraphs — first line is the one-line
     positioning, e.g. "A pack of 40 hand-drawn celestial vectors.")
   • Price + compare-at
   • Collections (e.g. "Graphics", "Vectors", "Textures")
   • Theme template: product.graphics

2. HERO METAFIELDS (namespace: disrupted)
   • badge_text — e.g. "40 vectors" or blank
   • tagline — one-line subhead

3. GRAPHICS SPECS METAOBJECT (disrupted.graphics_specs)
   • items: e.g. "40 vectors", "120 textures", "12 mockup files"
   • formats: e.g. "SVG · PNG · AI" or "PSD · PNG"
   • resolution: e.g. "Vector + 4096px PNGs"
   • license: "Personal · Commercial"
   • updates: "Lifetime"

4. FEATURES (disrupted.features — list of feature metaobjects, 2–4 entries)
   Each strip should showcase a category or usage:
   • What's inside (variety / count)
   • Quality / detail (close-ups)
   • Use cases / mockup contexts

5. GRAPHICS MANIFEST
   • Hero (2000×2000) — usually a hero arrangement of the pack
   • Gallery thumbs (2000×2000) × 3–7 — close-ups, use cases, variations
   • Feature strip images (1200×900) × N

6. GAPS — flag anything missing.

Format as one markdown doc, brutalist tone, tables, no fluff.
````

---

## Prompt 4 — BUNDLE products

Use for: any multi-product bundle (e.g. "Display Type Pack",
"Studio Starter Bundle").

````
I'm migrating a bundle product from <SOURCE STORE URL> into a new
Shopify storefront. I need a fill-out sheet for ONE bundle.

BUNDLE: <PRODUCT NAME>
SOURCE PDP: <SOURCE STORE URL>/products/<product-handle>

Visit the source PDP. The destination is a BUNDLE-template product.
Section order:

  main → bundle_contents → faq → related

The FAQ section is template-shared — SKIP FAQ research.

Output sections:

1. NATIVE SHOPIFY FIELDS
   • Title
   • Product type — "Bundle"
   • Description (2–4 short paragraphs)
   • Price — what the buyer pays for the bundle
   • Compare-at price — the SUM of individual prices (drives the
     strikethrough + auto savings calculation on the PDP)
   • Collections
   • Theme template: product.bundle

2. HERO METAFIELDS (namespace: disrupted)
   • badge_text — e.g. "Save 30%" or blank
   • tagline — one-line subhead

3. BUNDLE CONTENTS (disrupted.bundle_contents — product list)
   • List of Shopify products included
   • For EACH included product, note the source PDP URL so the
     destination can match the product reference

4. GRAPHICS MANIFEST
   • Hero (2000×2000) — bundle composition shot
   • Gallery thumbs (2000×2000) × 3–7

5. GAPS — flag missing content.

Skip features and CTAs — bundles use the bundle-contents section
instead of feature strips. Each included product has its own PDP
with its own features.

Format as one markdown doc, brutalist tone, tables, no fluff.
````

---

## Workflow when migrating an existing product

1. Pick the prompt that matches the product type (plugin / font / graphics / bundle).
2. Fill in `<SOURCE STORE URL>` (e.g. `https://studio2am.co/collections/blaine-pate-studio`).
3. Fill in `<PRODUCT NAME>` and the specific product URL.
4. Paste into a new Claude conversation. Wait for the fill-out sheet.
5. In Shopify admin, create the new product (or edit the existing one) and paste each section's content into the right field.
6. Assign the correct template (Theme template dropdown on the product edit page).
7. Set the relevant metafields (badge_text, tagline, specs metaobject, features list, etc.).
8. Save. Preview the PDP. Confirm every section either renders content or hides cleanly.

**Don't enter FAQs per-product** — they're shared across all products on the same template now. To edit them, go to theme editor → Customize → pick a product (any product) on that template → FAQ section → blocks. Changes apply to every product using that template.
