# ZYMARG WC Product Grid — Latest Version

> **Current version: `v1.5.2`**

---

## ⬇️ One-click download (latest)

**[Download "ZYMARG WC Product Grid v1.5.2.zip"](https://github.com/Aspeyash/ZYMARG-WC-Product-Grid/raw/refs/heads/feat/wishlist-no-fouc-and-rv-shortcode-v1.5.2/ZYMARG%20WC%20Product%20Grid%20v1.5.2.zip)**

> This v1.5.2 build currently lives on the branch `feat/wishlist-no-fouc-and-rv-shortcode-v1.5.2` (open for review via its Pull Request). After you merge the PR, the same file is available on the main line at the equivalent default-branch URL.

Install via **WordPress Admin → Plugins → Add New → Upload Plugin** (upgrades the existing copy in place).

---

## What's new in v1.5.2

- 🩹 **FOUC fix on Wishlist page.** The card stylesheet now lands in `<head>` as soon as the page is identified as containing a Wishlist / Recently Viewed shortcode (or as being a WooCommerce My Account page), so cards never paint unstyled and then snap into position. *Homepage and Elementor widget paths are unchanged.*
- ✨ **New shortcode `[zymarg_wcpg_recently_viewed]`.** Renders the visitor's recently-viewed products with the SAME card template as the homepage / search / wishlist / category pages. Reads from the existing tracker — no duplicate cookies, no new tables.
  - Attributes: `columns` (1–6, default 4), `limit` (1–20, default 20), `empty_message`
  - Filter: `zymarg_wcpg_recently_viewed_card_settings`
- No behaviour changes anywhere else.

### How to use the new shortcode
Drop `[zymarg_wcpg_recently_viewed]` into any page, or call it programmatically from a template:
```php
echo do_shortcode( '[zymarg_wcpg_recently_viewed columns="4" limit="12"]' );
```

---

## Recent versions

| Version | Date | Notes |
|---|---|---|
| **1.5.2** ← latest | 2026-06-26 | Fix wishlist FOUC + new `[zymarg_wcpg_recently_viewed]` shortcode |
| 1.5.1 | — | Wishlist shortcode |
| 1.4.5 | — | (older release) |

---

## How to always find the latest version
1. **This file (`LATEST.md`)** — top shows current version + one-click download.
2. **`VERSION`** at the repo root.
3. The newest `ZYMARG WC Product Grid vX.Y.Z.zip` committed at the repo root.
4. **`zymarg-wc-product-grid/zymarg-wc-product-grid.php`** — the `Version:` header inside the zip.
