# Warif Store — Build Brief

Hand this file to your coding agent as the spec. It describes what to build,
why, and how each piece should work.

---

## 1. What this is

A single-page storefront website for an Instagram-based fashion/accessories
shop called **Warif Store** (متجر وارف). There is no shopping cart and no
online checkout — visitors browse products and place orders by sending an
Instagram direct message. The site is fully static (plain HTML/CSS/JS, no
backend, no build step) so it can be hosted for free on **GitHub Pages**.

**Key constraint driving the whole architecture:** non-technical coworkers
need to be able to add, edit, or remove products without touching code. The
solution is to store product data in a **Google Sheet published as CSV**,
which the site fetches and renders client-side on every page load.

---

## 2. Requirements

### Functional
- Single HTML page, self-contained (inline CSS/JS is fine — no build tooling).
- Product data loads at runtime from a published Google Sheet CSV URL (via
  [PapaParse](https://www.papaparse.com/), loaded from a CDN).
- Sheet columns: `name`, `image`, `description`. Each row = one product card.
- **Prices are never shown on the site.** This is intentional — pricing is
  disclosed only when a customer messages via Instagram DM. Each product card
  shows a small note ("price on request" / `السعر عند التواصل`) instead of a
  price figure.
- If the CSV URL isn't set yet, or returns no rows, show a set of **demo
  placeholder products** (hardcoded in JS) so the page never looks broken or
  empty — with a small banner noting these are examples, not real inventory.
- If the CSV fails to load (bad URL, sheet not published), show a clear error
  state instead of a blank page.
- Each product card has an **"Order via DM"** button linking to
  `https://ig.me/m/<instagram_handle>` (opens Instagram direct messages).
- Instagram handle and the CSV URL are both top-of-file constants — the only
  two values a site owner should ever need to edit directly in code.
- Fully responsive down to mobile widths.
- Accessible: visible keyboard focus states, respects
  `prefers-reduced-motion`.

### Language & layout
- **Arabic language throughout** (all UI text, labels, buttons, empty/error
  states).
- **RTL (right-to-left) layout**: `<html lang="ar" dir="rtl">`. Prefer CSS
  logical properties (`inset-inline-start`, `margin-inline-start`, etc.) over
  physical `left`/`right` so the layout is robust if LTR support is ever
  added later.
- Product counts can use either Arabic-Indic or Western digits — Western
  digits are also common in Gulf/Levant e-commerce, so either is acceptable;
  just be consistent.

### Visual design
- **Palette — girly/rose/magenta**, not generic AI-cliché colors (avoid
  cream + terracotta, avoid black + neon-green). Suggested tokens:
  - `--ink: #3B0F2E` (deep plum, used for hero background & text-on-light accents)
  - `--ink-soft: #52173F`
  - `--blush: #FDF1F5` (main page background)
  - `--blush-dim: #F6DCE6` (card image backdrop, subtle fills)
  - `--magenta: #D6336C` (primary accent — buttons and highlights)
  - `--magenta-bright: #E8578C` (hover states, eyebrow text)
  - `--berry: #9C3D63` (secondary text accent)
  - `--ivory: #FFF8FA` (text on dark backgrounds)
- **Typography**: pair a characterful Arabic display face with a clean body
  face. Used here: **El Messiri** (headings, product names — has warmth and
  personality) + **Tajawal** (body text, buttons, labels — clean and highly
  legible at small sizes). Both are free on Google Fonts and have full Arabic
  character support.
- **Signature design element**: a small rotated "hang tag" badge (like a
  physical clothing tag) pinned to the top corner of each product photo,
  showing the item number. This ties the visual language back to fashion/
  garment retail rather than generic e-commerce.
- Product cards: photo (4:5 aspect ratio) → hang-tag badge → name (display
  font) + "price on request" note → short description → DM button (pill-shaped, magenta,
  darkens on hover).
- Hero section: dark plum gradient background, large display headline, short
  supporting line explaining the DM-to-order model, one CTA scrolling to the
  product grid.

### Content (for demo/placeholder data)
Use realistic but clearly fictional example products — Arabic names,
stock photography for images, and no visible prices. E.g. an oversized linen
top, a woven handbag, gold hoop earrings, a silk scarf. These exist so the
page never looks empty before the real sheet is connected, and should be
visibly labeled as examples.

---

## 3. File structure

```
warif-store/
├── index.html      # entire site — HTML, CSS, and JS in one file
└── README.md        # setup + usage instructions (see section 5)
```

No other files needed. No `package.json`, no build step — this should open
directly in a browser and work.

---

## 4. Deployment target

**GitHub Pages**, static hosting straight from a repo:
1. Push `index.html` (and `README.md`) to a GitHub repo.
2. Enable Pages in repo Settings → Pages → deploy from `main` branch, root.
3. Site is live at `https://<username>.github.io/<repo-name>/`.

No server, no environment variables, no secrets — everything needed to run
lives in the two constants at the top of `index.html`.

---

## 5. Non-technical workflow (the part that matters most)

This is the actual product requirement, more than any specific pixel: **a
person with zero coding ability must be able to manage the product catalog.**

The mechanism:
1. Site owner creates a Google Sheet with columns `name`, `image`,
   `description`.
2. Publishes it to the web as CSV (`File → Share → Publish to web`, choose
   the correct tab, format = CSV), and pastes that URL into
   `PRODUCTS_CSV_URL` in `index.html` once.
3. From then on, **anyone with edit access to the sheet** can add a row =
   add a product, delete a row = remove a product, reorder rows = reorder
   products. The website re-fetches the sheet on every page load — no
   publishing step, no redeploying, no code.
4. Product images need to be a direct image URL (not a webpage). Simplest
   options to hand to non-technical coworkers: Imgur upload → "copy image
   address," or a shared Google Drive image converted to
   `https://drive.google.com/uc?export=view&id=FILE_ID`.

Write this workflow up clearly in `README.md` in the same language as the
site (Arabic), since the person maintaining the catalog day-to-day is a
coworker, not a developer.

---

## 6. Explicitly out of scope

- No shopping cart, no payment processing, no checkout flow.
- No user accounts / login.
- No backend server or database — the Google Sheet **is** the database.
- No image hosting/upload built into the site — images are hosted externally
  and just linked.

If checkout is ever wanted later, the natural upgrade path is layering
**Shopify's Buy Button** or **Stripe Payment Links** onto the existing product
cards without a rebuild — worth a one-line mention in the README but not
something to build now.

---

## 7. Reference implementation

A working version of this spec already exists (Arabic, RTL, rose/magenta
palette, demo products, Google Sheets CSV integration via PapaParse). Use it
as the ground truth for structure and behavior if the agent needs a concrete
example to diff against, rather than reinventing the approach from scratch.
