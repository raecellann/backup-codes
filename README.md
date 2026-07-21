# backup-codes
# Cart Feature — File Map

---

## Cart Drawer (slides in from the right)

| File | What it does |
|------|-------------|
| `snippets/cart-drawer.liquid` | **Main file.** All drawer HTML, accessory logic, price calculations, and JS. Start here for any drawer bug. |
| `sections/cart-drawer.liquid` | Shopify section wrapper that renders the snippet above. Rarely needs editing. |
| `assets/cart-drawer.js` | Drawer open/close behavior. Also removes orphaned accessory surcharge items on every page load. |
| `assets/component-cart-drawer.css` | Drawer layout and visual styles. |
| `sections/cart-icon-bubble.liquid` | The cart icon in the header that opens the drawer. Shows item count. |

---

## /cart Page

| File | What it does |
|------|-------------|
| `sections/main-cart-items.liquid` | The product rows table on the cart page. |
| `sections/main-cart-footer.liquid` | Estimated total + checkout button on the cart page. |
| `assets/cart.js` | Handles quantity changes and item removal on the cart page. Also cleans up orphaned surcharge items when a product is removed. |
| `assets/component-cart-items.css` | Line item styles. |
| `assets/component-cart.css` | Shared cart styles used by both drawer and cart page. |
| `templates/cart.json` | Wires `main-cart-items` and `main-cart-footer` onto the `/cart` page. |

---

## Accessory Surcharge System

Accessories (e.g. Whip, Line Set Cover) are stored as item **properties** with prices like `(+$29.00)`.
A hidden `hvac-accessory-surcharge` product is added as a separate line item so the checkout total is correct.

| File | What it does |
|------|-------------|
| `assets/product-form.js` | Adds the surcharge line item when a product is added to cart. |
| `layout/theme.liquid` | Injects `window.hvacSurchargeVariants` — the list of surcharge price variants used by JS everywhere. |

---

## Common Errors & Where to Look

| Problem | File to check |
|---------|--------------|
| Estimated total wrong in drawer | `snippets/cart-drawer.liquid` — Liquid total calculation + `applyTrueTotal()` JS |
| Line item price (Total column) wrong | `snippets/cart-drawer.liquid` — `_display_final` Liquid variable + `price--end` element |
| Accessory price not added to total | `snippets/cart-drawer.liquid` — `_acc_extra_cents` / `_item_acc_cents` calculation |
| Total doesn't update after removing accessory | `snippets/cart-drawer.liquid` — `executeRemove()` function |
| Total doesn't update after saving accessory | `snippets/cart-drawer.liquid` — `saveAccessories()` / `postAndUpdate()` function |
| Stale surcharge items after page load | `assets/cart-drawer.js` — orphan cleanup IIFE at the bottom |
| Stale surcharge items after removing product | `assets/cart.js` — orphan cleanup inside `updateQuantity()` |
| Cart page total wrong | `sections/main-cart-footer.liquid` — `_footer_true_total` Liquid calculation |
| Add to cart doesn't add surcharge | `assets/product-form.js` — `addAccessoriesAsLineItems()` function |
| Surcharge variants not found | `layout/theme.liquid` — check `window.hvacSurchargeVariants` is populated |

---

## Key Concepts

- **`data-hvac-cart-total`** — the estimated total `<p>` element in the drawer footer. JS updates this directly.
- **`data-hvac-line-price`** — the per-line-item price element. Liquid renders base + accessory price.
- **`data-item-base`** — base product price in cents on each cart row `<tr>`.
- **`data-item-acc`** — accessory price in cents on each cart row `<tr>`. Updated by JS on change.
- **`hvac-accessory-surcharge`** — the Shopify product whose variants represent accessory prices. Must exist in the store.
