# Laravel Storefront Audit & Hardening Checklist

Detailed checklist to verify when auditing or bootstrapping a Laravel storefront project.

## A. Database & Seeding
- [ ] Run `php artisan migrate:status` and verify every table in `database.sqlite` / MySQL has a migration file.
- [ ] Run `php artisan test` with `DB_DATABASE=:memory:` in `phpunit.xml` and confirm `RefreshDatabase` succeeds from an empty DB.
- [ ] Verify `DatabaseSeeder` creates default admin, roles, settings, and catalog items idempotently.

## B. Routes, Auth & RBAC
- [ ] Hit `/admin` while logged out: must return `302` to `/admin/login` (never `500 Route [login] not defined`).
- [ ] Check `AdminAuthController::login`: exact `email`/`name` match only (no `LIKE '%...%'`), checks `is_active`, uses `RateLimiter`.
- [ ] Verify every `/admin/*` route group enforces permission middleware and restricted roles receive `403` on unauthorized modules.
- [ ] Verify `APP_DEBUG=false` in `.env` on any publicly reachable URL.

## C. Storefront Links, Forms & Checkout
- [ ] Check every category slug in navbar/footer against actual `categories.slug` rows.
- [ ] Verify blog cards link to `/blog/{slug}` and static CMS pages resolve at `/page/{slug}`.
- [ ] Check AJAX cart removal field names match controller validation (`row_id`).
- [ ] Verify checkout supports `multipart/form-data` (`FormData`) if design file uploads are enabled, and `order_items.design_file_path` is downloadable in `/status` and `/admin/orders/{id}`.
- [ ] Check `Setting::getWaNumber()` is used consistently in `CheckoutController`, topbar, hero CTAs, product detail, about page, and footer.

## D. Mobile UX & Assets
- [ ] Inspect mobile screenshot (`390x844`): topbar, search input + submit button, and scrollable nav bar must all be visible.
- [ ] Inspect product detail screenshot (`390x844`): image uses `aspect-[4/3]` + `object-contain` (no cropped edges) and opens a working zoom lightbox on click/tap.
- [ ] Run `npm run build` whenever Tailwind classes in `.blade.php` change.
- [ ] Confirm local media replacements/deletions unlink old files from `public/uploads/*` or `Storage::disk('public')`.
