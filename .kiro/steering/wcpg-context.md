---
inclusion: always
---

# ZYMARG WC Product Grid — repo context

## Purpose
The **canonical product-card design** for the entire ZYMARG ecosystem AND the **owner of the visitor's Recently Viewed + Wishlist data**. Anywhere a ZYMARG site renders a product card with matching design, it routes through this plugin.

## Place in the four-piece ZYMARG ecosystem
- **ZYMARG OS theme** (`Aspeyash/Wordpress-Theme`) — presentation shell, calls this plugin's shortcodes/templates.
- **ZYMARG Connection Engine** (`Aspeyash/Connection-Engine`) — category takeover + site-wide card rule. **Only consumes** this plugin's public API (`Template_Loader::get_template_html`). Must NEVER modify this plugin.
- **ZYMARG Insights** (`Aspeyash/ZYMARG-Insights`) — analytics. Defers to this plugin for the Recently Viewed list when active.

## Source of truth files
- **Card template**: `templates/grid/product-card.php` — the canonical card markup. Override pattern: themes can place `zymarg-wcpg/grid/product-card.php` to override.
- **Card sub-templates**: `templates/grid/grid-wrapper.php`, `vendor.php`, `price.php`, `rating-soldcount.php`, `badges.php`, `parts/empty-state.php`.
- **Template loader**: `includes/class-template-loader.php` — `Template_Loader::get_template()` and `::get_template_html()`. Theme-override aware (looks in `theme/zymarg-wcpg/...` first).
- **Default card settings recipe**: each consumer (Connection Engine, wishlist shortcode, RV shortcode) defines its own canonical card-settings array. Keep them in sync when adding new card features.

## Tracker — Recently Viewed
- File: `includes/Tracking/class-recently-viewed.php`
- Stores: user_meta `_zymarg_recently_viewed` (logged-in), cookie `zymarg_rv` (1yr TTL, guests), WC session mirror.
- Public API: `\Zymarg\WCPG\Tracking\Recently_Viewed::get_ids()` and `::record($product_id)`.
- Trigger: `template_redirect` priority 20 on every single product page view.

## Wishlist data store
- `includes/Wishlist/class-wishlist-store.php` — `Wishlist_Store::get_ids()`, `::has($id)`, etc.
- `includes/Wishlist/class-wishlist-merge.php` — merges guest cookie into user meta on login.
- User meta key: `Wishlist_Store::USER_META_KEY`.

## Public shortcodes
- `[zymarg_wcpg_wishlist]` — `columns="3"` default, accepts `empty_message`. From v1.5.1+.
- `[zymarg_wcpg_recently_viewed]` — `columns="4"` `limit="20"` defaults, accepts `empty_message`. From v1.5.2+.

## FOUC fix (v1.5.2+)
File: `includes/class-shortcode-asset-preloader.php`. Hooks `wp_enqueue_scripts` at **priority 5** (before wp_head fires at 10) and eagerly enqueues the frontend stylesheet when either:
- the queried post contains either of the two shortcodes, OR
- the current request is a WooCommerce My Account page (`is_account_page()`).

No-op everywhere else, so the Elementor widget path and homepage are unchanged.

## Empty-state copy (Option A — v1.5.3+ brand voice)
- Wishlist: *"Nothing saved yet. Tap the heart on anything you love and it'll be waiting for you here — across all your devices."* Button: **Browse products**.
- Recently Viewed: *"You're new here. The products you look at will live in this space — across all your devices."* Button: **Start exploring**.

## Asset architecture
- All JS uses `strategy=>defer` (WP 6.3+) with graceful fallback to `in_footer => true` (WP 6.2).
- Handles registered in `includes/class-assets.php`: `zymarg-wcpg-frontend`, `zymarg-wcpg-quickview`, `zymarg-wcpg-slider`, `zymarg-wcpg-editor`, `zymarg-wcpg-wishlist-button`.
- Swiper resolved via `Assets::resolve_swiper_handle()` — prefers Elementor Pro's `swiper`, falls back to local bundle, last-resort jsDelivr.

## Repo convention (IMPORTANT)
- This repo **tracks only zip builds and CI yaml** — source code is NOT committed file-by-file. The source lives in each zip.
- Local development pattern: extract the latest zip to `_extracted/zymarg-wc-product-grid/`, edit there, rebuild the zip, commit zip + LATEST.md + VERSION.
- `.github/workflows/version-drift-check.yml` validates that the plugin header version, `ZYMARG_WCPG_VERSION` constant, and readme `Stable tag` all match on every push/PR. **Build fails on drift** — keep all three aligned on every bump.

## Version anchors that MUST stay aligned
1. `zymarg-wc-product-grid.php` plugin header → ` * Version:           X.Y.Z`
2. `zymarg-wc-product-grid.php` → `define( 'ZYMARG_WCPG_VERSION', 'X.Y.Z' );`
3. `readme.txt` → `Stable tag: X.Y.Z` + new `== Changelog ==` entry
4. Repo-root `VERSION` file → `X.Y.Z`
5. Repo-root `LATEST.md` → bumped current version + download URL
6. Built zip filename → `ZYMARG WC Product Grid vX.Y.Z.zip` (note: spaces and capitalization in filename)

## Recent open PR stack (verify current state)
- PR #14 — **v1.5.2** wishlist FOUC fix + new `[zymarg_wcpg_recently_viewed]` shortcode
- PR #15 — **v1.5.3** Option-A empty-state copy on both shortcodes

## Critical rule
**This is the user's most important plugin — the design source of truth.** Any changes here must be (1) explicitly approved, (2) opt-in/additive when possible, (3) shipped on a branch + PR + zip for review, (4) never break the homepage/Elementor widget path.
