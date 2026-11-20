---
name: webp-catalog-assets
description: Generate uniform, lightweight branded WebP product catalog cards (800x600), hero banners (1280x720), and blog thumbnails (960x540) using generate_image, HTML/CSS/SVG headless Chromium rendering, and Pillow WebP compression—no Canva connector required.
---

# WebP Catalog Assets & Uniform Product Card Pipeline

Use this skill whenever a web storefront or e-commerce catalog needs cohesive, branded, lightweight `.webp` visuals without external design tools (such as Canva).

## 1. Standard Sizes & Compression Targets

Always resize with `Image.Resampling.LANCZOS` and save with `format="WEBP", method=6`:

- **Hero Banners (`16:9`):** `1280 x 720 px`, `quality=82` (target **40–120 KB**).
- **Product Catalog Cards (`4:3`):** `800 x 600 px`, `quality=84` (target **13–45 KB**).
- **Blog / Article Thumbnails (`16:9`):** `960 x 540 px`, `quality=82` (target **35–75 KB**).

## 2. Image Generation Rules (`generate_image`)

- **Hero Banners (`AspectRatio: "16:9"`):**
  - When text/CTA buttons are rendered by HTML/Blade over the banner, generate **clean photography/artwork with zero baked-in text or logos**.
  - Request **dark moody studio shadows on the left third** (copy-space for white overlay text) and the illuminated main subject on the center-right.
- **Product Studio Shots (`AspectRatio: "4:3"`):**
  - Use a consistent **bright soft light-gray seamless studio backdrop (`#F4F6F9`)** with soft natural contact shadow, generous margins, and **zero text or watermarks**.

## 3. Uniform Branded Card Template (HTML + Chromium `@2x` + Pillow)

Instead of putting text overlays directly inside `generate_image` (which risks typos and inconsistent framing), wrap each product photo inside a standardized HTML/CSS/SVG template:

1. **Leave top corners clear** (e.g. `padding-top: 46px`, inner hairline border) so Blade/UI corner badges (`Category`, `Best Seller`, `Zoom`) never collide with template artwork.
2. **Use `object-fit: contain` + `mix-blend-mode: multiply`** on the centered product photo so tall items (X-banners, roll-ups, flags) and wide items (banners, boxes) both fit uncropped.
3. **Place brand identity & product specs in a bottom bar** (e.g. `58px` dark slate `#0F172A` with a `3px` brand accent line, crisp SVG logo, CMYK/brand dots, and a per-product spec pill like `OUTDOOR FLEXI • 3.2M HIGH-RES`).
4. **Render at 2× HiDPI** with headless Chromium (`--force-device-scale-factor=2 --window-size=800,600`), then downsample in Python Pillow to `800 x 600` `.webp`.

## 4. Storefront Display & Verification Checklist

1. **Match container aspect ratios:** Ensure both catalog grid cards and product detail pages use `aspect-[4/3]` with `object-contain` (never `aspect-square` + `object-cover`, which crops edges on mobile).
2. **Enable click-to-zoom lightbox:** Provide a modal lightbox on product detail views so mobile users can inspect fine print/material details.
3. **Rebuild assets:** If Blade Tailwind classes changed, run `npm run build` (Vite) and `php artisan optimize:clear`.
4. **Verify live:** Check `HTTP 200` on every `.webp` URL and capture a mobile (`390x844`) + desktop (`1280x900`) Chromium screenshot to confirm zero clipping or badge overlap before committing to Git.
