---
name: laravel-storefront
description: Mandatory engineering, mobile-UX, security, and verification rules for building or auditing Laravel storefront/web-to-print applications (migrations, auth/RBAC, mobile header & zoomable 4:3 images, dynamic settings, media cleanup, and live Chromium checks). Use when creating, auditing, or fixing any Laravel e-commerce or catalog web app.
---

# Laravel Storefront & Admin Standards

Apply these mandatory rules on every Laravel storefront build or audit so recurring bugs never happen twice. For a detailed audit walkthrough, see `references/audit-checklist.md`.

## 1. Database, Migrations & Testing Isolation

- **Every table must have a migration & seeder:** Never create tables manually in SQLite/MySQL without a matching Laravel migration. Use idempotent guards (`if (! Schema::hasTable(...))`) when retrofitting existing live databases.
- **Deduplicate unique slugs:** On `store`/`update` for products, categories, articles, and pages, always append a short random suffix when `Model::where('slug', $slug)->exists()` to avoid SQL `UNIQUE constraint failed` 500 errors.
- **Isolate PHPUnit (`phpunit.xml`):** Always enable `<env name="DB_CONNECTION" value="sqlite"/>` and `<env name="DB_DATABASE" value=":memory:"/>` before running `RefreshDatabase` tests so tests never wipe a live `database.sqlite`.

## 2. Authentication, RBAC & Public Safety

- **No wildcard `LIKE` in login:** Match `email` or `name` strictly (`=`), verify `$user->is_active`, and protect `POST /admin/login` with `RateLimiter`.
- **Configure guest redirect:** In `bootstrap/app.php`, set `$middleware->redirectGuestsTo(fn () => route('admin.login'))` whenever admin login uses `admin.login` instead of `login` (prevents `500 Route [login] not defined` on `/admin`).
- **Real RBAC, no `alert()` stubs:** Every admin section (Users, Roles, Permissions, Menus, Pages) must persist to database tables and enforce route-level permission middleware (`admin.perm:...`). Never leave hardcoded arrays or `onclick="alert(...)"` placeholders.
- **No weak hardcoded passwords & debug off:** Read seeder passwords from `env('ADMIN_DEFAULT_PASSWORD')`, and ensure `APP_DEBUG=false` on public URLs.

## 3. Dynamic Settings & Media Hygiene

- **Single source for contact/WA/hours:** All WhatsApp links, checkout redirects, topbar contact labels, about pages, and operating hours must read from `Setting::getWaNumber()` / `Setting::get(...)`—never hardcode dummy phone numbers in Blade or controllers.
- **Clean up replaced/deleted local media:** Whenever a product thumbnail, banner, or article image is replaced or deleted, delete the old local file safely (`public/uploads/*` or `Storage::disk('public')`) while ignoring external `http(s)://` URLs.

## 4. Mobile-First Storefront & Image UX

- **Never hide search or primary nav on mobile:** Keep the search bar and sub-navigation (`Home`, `Semua Produk`, `Kategori`, `Blog`, etc.) visible on `< 768px` screens (e.g. full-width mobile search row + horizontally scrollable nav bar with an unclipped mobile category panel).
- **Verify all category/nav slugs against DB:** Confirm every hardcoded navbar/footer category link and detail link exists in `categories`/`products`, and wire clickable article detail routes (`/blog/{slug}`) whenever cards advertise *"Baca Selengkapnya"*.
- **Match `4:3` containers & add click-to-zoom:** Product detail pages must match catalog card proportions (`aspect-[4/3]` + `object-contain`, NOT `aspect-square` + `object-cover` which crops edges on mobile) and include a fullscreen **lightbox zoom** modal on tap/click.
- **Prevent duplicate CTA arrows:** Strip literal `→` from database `button_text` before rendering an arrow icon next to it.

## 5. Build & Verification Gate (Before Reporting Done)

1. Run `npm run build` after any Blade/CSS change (Tailwind Vite bundles only used classes!) and `php artisan optimize:clear`.
2. Run `php artisan test` and confirm all tests pass.
3. Verify HTTP `200` (and `302` for guest `/admin`) across all public & filter routes.
4. Capture and visually inspect a **mobile (`390x844`)** and **desktop** headless Chromium screenshot to confirm no hidden header, no cropped product images, and no overlapping badges.
