# Shopify Dawn Custom Theme

[![Theme Check](https://github.com/MichaelEnny/shopify-dawn-custom-theme/actions/workflows/theme-check.yml/badge.svg)](https://github.com/MichaelEnny/shopify-dawn-custom-theme/actions/workflows/theme-check.yml)

A customized Shopify Online Store 2.0 theme built on top of [Shopify Dawn](https://github.com/Shopify/dawn), using Liquid, HTML, CSS, and JavaScript to improve storefront usability, mobile responsiveness, SEO structure, and theme maintainability.

---

## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
- [Custom Sections](#custom-sections)
- [Cart Drawer Enhancements](#cart-drawer-enhancements)
- [SEO Improvements](#seo-improvements)
- [Performance Notes](#performance-notes)
- [CI/CD – Theme Check](#cicd--theme-check)
- [Customization Guide](#customization-guide)
- [File Structure](#file-structure)

---

## Features

### Custom Homepage Sections

| Section | File | Description |
|---|---|---|
| Promotional Banner | `sections/custom-promotional-banner.liquid` | Full-width CTA banner with badge, heading, subtext, and button |
| Trust Badges | `sections/custom-trust-badges.liquid` | 2×2 (mobile) / 4-column (desktop) trust icon grid |
| Product Highlights | `sections/custom-product-highlights.liquid` | Curated product grid with badges, feature text, and CTAs |

### Mobile-First Product Grid

- 2-column mobile grid via `grid--2-col-tablet-down`
- Responsive product cards with optimized spacing via `custom-sections.css`
- `srcset`-based responsive images on Product Highlights cards
- Quick Add button enabled on the Featured Collection section
- Swipe-on-mobile enabled for better touch browsing

### Product Recommendation Block

The existing `sections/related-products.liquid` (Dawn built-in) is preserved and wired into the product template. It uses Shopify's native `product-recommendations` API to surface upsell and cross-sell products automatically.

### Cart Drawer Enhancements

- **Free shipping progress bar** — dynamically computed in Liquid against a configurable threshold (`free_shipping_threshold` in `snippets/cart-drawer.liquid`, default $50.00)
- Progress bar turns green and message updates when the threshold is reached
- ARIA `progressbar` role with `aria-valuenow` for accessibility

### SEO Improvements

- **JSON-LD structured data** added to `snippets/meta-tags.liquid`:
  - `WebSite` schema with `SearchAction` on every page
  - `Organization` schema with social links
  - `Product` schema on product pages (price, availability, brand, aggregate rating)
  - `Article` schema on blog post pages
- Open Graph and Twitter Card tags (Dawn default, preserved)
- Responsive images via `srcset` on all custom section images
- Semantic heading hierarchy (`h2` → `h3`) inside custom sections
- Descriptive `alt` attributes pulled from Shopify image metadata with title fallback

### Performance Notes

| Area | Approach |
|---|---|
| CSS | Custom styles isolated to `assets/custom-sections.css`; loaded only where needed |
| Images | `srcset` + `sizes` on Product Highlights; `loading="lazy"` on all non-hero images |
| JavaScript | No additional JS added; custom sections are pure Liquid + CSS |
| Markup | No extra wrapper divs; sections use Dawn's existing `page-width` class |

**Before / After (estimated impact):**

- Removed `video` section from default homepage template (optional – reduces LCP blocking on slow connections)
- `quick_add: standard` on Featured Collection avoids full page reload for add-to-cart

### CI/CD – Theme Check

GitHub Actions workflow at `.github/workflows/theme-check.yml` runs [Shopify Theme Check](https://github.com/Shopify/theme-check) on every push and pull request to `main`/`master`. This validates:

- Liquid syntax errors
- Missing required schema fields
- Deprecated Liquid filters
- Asset reference integrity

---

## Getting Started

### Prerequisites

- [Shopify CLI](https://shopify.dev/docs/storefronts/themes/tools/cli) v3+
- Node.js 18+
- A Shopify Partner account and development store

### Installation

```bash
# Clone this repo
git clone https://github.com/MichaelEnny/shopify-dawn-custom-theme.git
cd shopify-dawn-custom-theme

# Authenticate with your store
shopify auth login --store your-store.myshopify.com

# Push and preview the theme
shopify theme dev --store your-store.myshopify.com --path ./dawn
```

### Deploying to Production

```bash
shopify theme push --store your-store.myshopify.com --path ./dawn
```

---

## Custom Sections

### Promotional Banner (`custom-promotional-banner`)

**Location:** `sections/custom-promotional-banner.liquid`

**Schema settings:**

| Setting | Type | Default | Description |
|---|---|---|---|
| `badge_text` | text | — | Small pill label above heading |
| `title` | inline_richtext | "Limited Time Offer" | Section heading |
| `heading_size` | select | `h1` | h0 / h1 / h2 |
| `text` | text | — | Subtext paragraph |
| `button_label` | text | "Shop Now" | CTA button text |
| `button_link` | url | — | CTA destination |
| `button_style_secondary` | checkbox | false | Outline vs filled button |
| `color_scheme` | color_scheme | scheme-2 | Theme color scheme |
| `padding_top/bottom` | range | 40px | Section spacing |

---

### Trust Badges (`custom-trust-badges`)

**Location:** `sections/custom-trust-badges.liquid`

Renders up to 6 trust badge blocks in a responsive grid. Each block supports a built-in SVG icon (shipping, secure, returns, support, gift, checkmark) or a custom SVG string.

**Block settings:**

| Setting | Type | Description |
|---|---|---|
| `icon_type` | select | Built-in icon |
| `custom_icon` | html | Override with custom SVG |
| `title` | text | Badge title |
| `text` | text | Badge description |

---

### Product Highlights (`custom-product-highlights`)

**Location:** `sections/custom-product-highlights.liquid`

Renders up to 6 product cards with optional badge labels, feature copy, and individual CTAs. Cards show hover image zoom and a "View all" button can be configured at the section level.

**Block settings:**

| Setting | Type | Description |
|---|---|---|
| `product` | product | Shopify product picker |
| `badge_label` | text | e.g. "Best Seller", "New" |
| `feature_text` | text | One-line benefit copy |
| `cta_label` | text | Button text (default: "View Product") |

---

## Cart Drawer Enhancements

The free shipping threshold is set in `snippets/cart-drawer.liquid`:

```liquid
{%- assign free_shipping_threshold = 5000 -%}  {%- comment -%} in cents {%- endcomment -%}
```

Change `5000` to any value in cents (e.g. `7500` for $75.00) to update the target across the entire cart experience.

---

## SEO Improvements

JSON-LD is injected from `snippets/meta-tags.liquid`, which is rendered inside `<head>` via `layout/theme.liquid`. No additional layout changes are required.

To verify structured data: paste a product URL into [Google's Rich Results Test](https://search.google.com/test/rich-results).

---

## File Structure

```
dawn/
├── assets/
│   └── custom-sections.css          # All styles for custom sections + cart enhancements
├── sections/
│   ├── custom-promotional-banner.liquid
│   ├── custom-product-highlights.liquid
│   └── custom-trust-badges.liquid
├── snippets/
│   ├── cart-drawer.liquid            # Modified: free shipping bar added
│   └── meta-tags.liquid              # Modified: JSON-LD structured data added
└── templates/
    └── index.json                    # Modified: custom sections added to homepage

.github/
└── workflows/
    └── theme-check.yml               # GitHub Actions: Shopify Theme Check on push/PR
```

---

## Customization Guide

- **Change color scheme:** each custom section exposes a `color_scheme` setting in the theme editor
- **Adjust free shipping threshold:** edit `free_shipping_threshold` in `snippets/cart-drawer.liquid`
- **Add more trust badges:** up to 6 blocks supported; drag-and-drop in the theme editor
- **Add more product highlights:** up to 6 blocks; assign products and badges per block
- **Modify grid columns:** the highlights grid is 1 → 2 → 3 columns at mobile / tablet / desktop breakpoints, controlled in `assets/custom-sections.css`

---

## License

Based on [Shopify Dawn](https://github.com/Shopify/dawn) — [MIT License](LICENSE.md).
Customizations by MichaelEnny.
