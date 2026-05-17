# Launch checklist — disrupted-minds-studio.myshopify.com

Comprehensive game plan for taking the theme live on the real merchant store.
Order matters: definitions BEFORE imports, otherwise CSV metafield columns
silently fail and you'll have to re-import.

---

## Phase A — Foundation (~30 min)

- [ ] **Pick a Shopify plan** (Basic minimum to take orders). Admin → Settings → Plan.
- [ ] **Sender email**: Admin → Settings → Store details → set to a real email you check.
- [ ] **General**: store name, contact email, store address.
- [ ] **Payment provider**: Settings → Payments → enable Shopify Payments (or other).
- [ ] **Taxes** (digital goods are usually simple but check your jurisdiction): Settings → Taxes.
- [ ] **Shipping**: N/A for digital — but you still need to set "No shipping required" on products OR use Digital Downloads app for fulfillment.

---

## Phase B — Data definitions (~45 min)

These need to exist BEFORE you import products, or metafield data won't attach.

### B1 — Metaobject definitions

Path: Admin → **Settings → Custom data → Metaobjects → Add definition**

For each one below: pick "Allow this metaobject as a list" only where noted.
Field handles must match exactly (these are what the Liquid sections read).

| Metaobject | Field handle | Field type | Notes |
|---|---|---|---|
| **Feature** | `label` | Single line text | Optional eyebrow ("01 / In studio") |
| | `title` | Single line text | Required |
| | `body` | Multi-line text | |
| | `images` | File reference (list) | Multiple images |
| | `captions` | Single line text (list) | One per image |
| **FAQ item** | `question` | Single line text | |
| | `answer` | Multi-line text | |
| **CTA inset** | `eyebrow` | Single line text | |
| | `heading` | Single line text | |
| | `body` | Multi-line text | |
| | `button_label` | Single line text | |
| | `button_url` | URL | |
| | `image` | File reference | |
| **Video** | `url` | URL | YouTube/Vimeo link |
| | `file` | File reference | Uploaded mp4 |
| | `thumbnail` | File reference | Poster image |
| | `heading` | Single line text | |
| | `subhead` | Single line text | |
| | `meta_text` | Single line text | Corner label |
| | `duration` | Single line text | "90s" |
| **Plugin specs** | `compat` | Single line text | "Photoshop 2022+" |
| | `format` | Single line text | "UXP" |
| | `file_size` | Single line text | "2.4 MB" |
| | `license` | Single line text | "Personal & commercial" |
| | `updates` | Single line text | "Lifetime" |
| | `support` | Single line text | "Email" |
| **Font specs** | `family` | Single line text | "6 weights · 1 italic" |
| | `glyphs` | Single line text | "412 per weight" |
| | `formats` | Single line text | "OTF / TTF / WOFF2" |
| | `language` | Single line text | "Latin Extended" |
| | `license` | Single line text | "Personal · Print · Web" |
| **Graphics specs** | `items` | Single line text | "40 graphics" |
| | `formats` | Single line text | "SVG / PNG" |
| | `resolution` | Single line text | "Vector + 4096px" |
| | `license` | Single line text | "Personal & commercial" |
| | `updates` | Single line text | "Lifetime" |

### B2 — Product metafield definitions

Path: Admin → **Settings → Custom data → Products → Add definition**

| Namespace.Key | Type | Notes |
|---|---|---|
| `disrupted.features` | Metaobject (Feature) — **List** | Multiple feature strips per PDP |
| `disrupted.faq` | Metaobject (FAQ item) — **List** | |
| `disrupted.ctas` | Metaobject (CTA inset) — **List** | |
| `disrupted.video` | Metaobject (Video) — single | |
| `disrupted.plugin_specs` | Metaobject (Plugin specs) — single | Used on plugin PDPs |
| `disrupted.font_specs` | Metaobject (Font specs) — single | Used on font PDPs |
| `disrupted.graphics_specs` | Metaobject (Graphics specs) — single | Used on graphics PDPs |
| `disrupted.font_file` | File reference | .woff2 / .ttf / .otf for type specimen section |
| `disrupted.bundle_contents` | Product — **List** | What's-included on bundle PDPs |
| `disrupted.demo_url` | Single line text | Sub-CTA destination (supports `#section` anchors) |
| `disrupted.sub_cta_label` | Single line text | Optional sub-CTA label override (defaults to "Watch the demo") |
| `disrupted.badge_text` | Single line text | Optional badge override |
| `disrupted.tagline` | Single line text | Short product tagline shown under title |

---

## Phase C — Products & content (~variable)

### C1 — Products

If importing via CSV:
- [ ] Verify your CSV includes Metafield columns in this format:
      `Metafield: disrupted.tagline [single_line_text_field]`
      `Metafield: disrupted.features [list.metaobject_reference]`
- [ ] Admin → Products → Import → upload CSV
- [ ] After import, spot-check 1-2 products to confirm metafields populated

If creating manually:
- [ ] For each product set: Title, Description, Price, Compare-at price (where applicable), **Product type**, Tags, Images, Theme template (right sidebar)
- [ ] Then in the Metafields panel: fill in whichever apply

### C2 — Product types (drives Bundle badge + collection filtering)

Suggested type strings (must be consistent):
- `Photoshop Plugin` — BACKLIT, FLUX, future plugins
- `Display Typeface` / `Mono Typeface` — fonts
- `Graphics Pack` — graphics/mockups
- `Bundle` — bundle products (triggers the BUNDLE badge automatically)

### C3 — Theme template per product

Right sidebar of each product → "Theme template":

| Product type | Template |
|---|---|
| Plugins | `product.plugin` |
| Fonts | `product.font` |
| Graphics | `product` (default) |
| Bundles | `product.bundle` |

### C4 — Files

- [ ] Upload each product's deliverable file (ZIP, .woff2, .ccx, etc.) via the digital download app or as Files
- [ ] For fonts: also upload the .woff2 to the product's `disrupted.font_file` metafield so the type specimen section renders

### C5 — About page

- [ ] Admin → Online Store → Pages → **Add page**
- [ ] Title: `About` · Handle: `about` · Visibility: Visible
- [ ] Theme template (right sidebar): `page.about`
- [ ] Body: leave blank (content comes from theme editor)
- [ ] Save → then customize via theme editor: Customize → top dropdown → "About page" → Disrupted about section → edit founder name, email, social links, etc.

---

## Phase D — Navigation & filtering (~30 min)

### D1 — Smart collections

Admin → Products → Collections → **Create collection** (Smart)

| Title | Condition |
|---|---|
| All products | (uses auto `/collections/all`) |
| Plugins | Product type = `Photoshop Plugin` |
| Fonts | Product type = `Display Typeface` OR `Mono Typeface` |
| Graphics | Product type = `Graphics Pack` |
| Bundles | Product type = `Bundle` |

### D2 — Main menu

Admin → Online Store → Navigation → **Main menu**

Suggested order:
- SHOP → `/collections/all`
- PLUGINS → `/collections/plugins`
- FONTS → `/collections/fonts`
- GRAPHICS → `/collections/graphics`
- BUNDLES → `/collections/bundles`
- CONTACT → `/pages/about`

### D3 — Footer menu

Optional but expected:
- Refunds / Terms / Privacy → `/policies/refund-policy` etc.
- Free assets, Bundles, etc.

### D4 — Search & Discovery filters

Install **Search & Discovery** app if not already (free, Shopify-native).

- [ ] Apps → Search & Discovery → **Filters** tab → Add filter
- [ ] Add: **Price**, **Product type**, **Tag**, **Availability**
- [ ] Save

### D5 — Theme settings (color schemes, fonts)

These travel with the theme. After GitHub deploys the theme to the real
store, open Customize → Theme settings — should already show:

- Color schemes (scheme-1 = black bg, off-white text, orange accent)
- Body font: Inter
- Accent font: IBM Plex Mono
- Verify nothing got reset to defaults during the deploy

---

## Phase E — Pre-launch test (~30 min)

- [ ] Open the unpublished theme's preview URL (not the live store)
- [ ] Click through every nav item — every collection loads, no errors
- [ ] Open one product — gallery thumbs swap, ATC works, cart drawer opens with item
- [ ] Test the search dropdown — type a product, see results, click through
- [ ] Submit the contact form — verify the email arrives
- [ ] Test on a real phone (not just DevTools) — every page, every flow
- [ ] **Run a $1 test order with a real card** in test mode (Settings → Payments → Test mode), refund yourself after. This catches any payment misconfig BEFORE customers do.

---

## Phase F — Launch

- [ ] **Storefront password OFF**: Admin → Online Store → Preferences → uncheck "Restrict access"
- [ ] **Publish the theme**: Themes → ⋯ on Disrupted Studio theme → Publish
  - Instant atomic swap. Old theme becomes unpublished (rollback available)
- [ ] Visit the live URL in a private window to confirm
- [ ] Tweet / announce
- [ ] Watch the first orders come in, fix anything that breaks immediately

---

## Future updates (after launch)

Workflow now that GitHub is connected:

1. Make code changes locally
2. `git add . && git commit -m "..." && git push origin custom/disrupted-studio`
3. ~30 seconds later the theme on the merchant store updates (unpublished theme reflects changes)
4. Preview on the unpublished theme
5. If happy: republish (no action needed if you're pushing to the
   already-published branch — it deploys automatically). For risky
   changes: branch off, push the branch, get a separate preview theme.

For risky changes:
1. `git checkout -b feature/xyz`
2. Make changes, commit, push the new branch
3. Shopify auto-creates a separate preview theme for that branch
4. Verify in preview
5. Merge to `custom/disrupted-studio` to deploy

---

## Reference

- Dev store: `disrupted-studio.myshopify.com` (Partners, password `iadren`)
- Real store: `disrupted-minds-studio.myshopify.com` (owner `blainepate2002@gmail.com`)
- GitHub: `blainepatedeisgn/horizon` branch `custom/disrupted-studio`
- Local repo: `~/Desktop/disrupted-studio-theme/`
