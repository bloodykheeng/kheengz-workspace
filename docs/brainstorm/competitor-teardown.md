# Competitor teardown

What the big platforms do, what we take, and what we leave. Researched 15 Nov 2026. Vendor marketing was
treated as a list of ideas, not proof; the sources are at the bottom.

## The platforms

### Bagisto (Laravel, open source core)
- **Core (free):** products with variants (configurable), **inventory sources** (stock per warehouse/shop),
  **channels** (several storefronts, each with its own domain, theme, currency and locale from one admin),
  customer groups, cart rules / coupons, RMA (returns), B2B extension, headless API, a Next.js storefront.
- **Multi-vendor is a paid Webkul extension**, downloaded as a zip and merged in, not a composer package.
  Admin sets a **global commission** and **per-seller overrides**. One customer order is **split into seller
  orders**, each with its own invoice and shipment, and commission is worked out per seller order. Sellers see
  earnings and **request payouts**; the admin pays after commission.
- **Take:** split orders, per-seller commission override, inventory per location, channels idea (later: one
  install, several storefronts).
- **Leave:** zip-merged paid extensions, a monolith admin we can't shape, EAV attributes and JSON columns.

### CS-Cart Multi-Vendor (PHP, commercial)
- Sold as a **separate product** from CS-Cart (single store): same core, marketplace bolted on. Pricing seen
  around $725/year or ~$3,590 lifetime (unconfirmed on their own page).
- **Vendor plans**: a monthly fee, a commission rate, a product limit and allowed categories per plan. This is
  how the marketplace owner earns.
- **Vendor panel**: products, orders, shipping methods, own branding (a mini storefront page).
- **Single checkout** across vendors, **split payments** (Stripe Connect / PayPal), commissions automated, or
  the vendor collects and pays the marketplace on a set date.
- **Take:** vendor plans (Phase 2), vendor storefront page, single checkout, the two money flows (marketplace
  collects and pays out, or vendor collects and owes commission).
- **Leave:** two separate products. We ship **one codebase** where single vendor is a setting.

### Shopify (SaaS, the UX bar)
- **"Buy it now"** beside "Add to cart" on every product: skips the cart, straight to checkout.
- One-page checkout, address autocomplete, Shop Pay remembers the buyer by phone/email with a code, no password.
- Theme editor: the merchant changes colours, logo and sections without code.
- **Take:** Buy now, one-page checkout, "remember me by phone + code" instead of passwords, merchant-editable
  brand (colours, logo, banner) through tokens.

### Jumia Uganda (the local reality)
- **Cash on delivery won:** Jumia's own reports put it at **83%** of Ugandan shoppers. Card on delivery came later
  (riders carrying POS), then Mobile Money and JumiaPay.
- Their MD (Nile Post, 2026): customers order, **inspect the product at the pickup station, then pay**. This is
  what built trust.
- Pickup stations, free next-day delivery in Kampala and Entebbe for express items, Jumia Prime subscription.
- **Take:** pay on delivery as a first-class method (not an afterthought), pickup points, inspect before paying,
  delivery fee by area.

### Lunar (Laravel package, headless)
- Products, variants, price per currency and customer group, carts, orders, taxes, a Filament admin. 1.x stable
  (1.3 in Jan 2026, 1.5 beta in Apr 2026), 2.0 in alpha.
- **Leave as a dependency:** bigint ids (we use ULIDs), JSON `attribute_data` (our no-JSON-columns rule), a
  Filament admin we would not use, and its own conventions everywhere. **Read its schema** as a reference for
  pricing and cart calculation when we design ours.

### Others worth one idea each
- **Amazon / Takealot:** many sellers can offer **the same product** (one product page, a "buy box", other
  offers below). Phase 3 at most; MVP is one product = one vendor.
- **Etsy:** each seller has a shop page with their story, reviews and policies. Good for vendor trust.
- **WhatsApp commerce (common in Kampala):** "Order on WhatsApp" with the cart as a message. Cheap to add,
  shoppers trust it.

## What ours does that they don't (the pitch)

1. **One codebase, both editions.** A shop is a marketplace with one vendor. Flip a setting and invite sellers.
   No migration, no second product.
2. **Built for how East Africa pays:** Mobile Money prompt, pay on delivery, pickup and inspect, card later.
   Not a Stripe-first platform with Mobile Money glued on.
3. **No login to buy.** Phone number, name, where to deliver, done. Track the order with phone + order number.
   An account is offered after the order, never before.
4. **Buy now or cart, the buyer's choice.**
5. **Light on data:** fast pages on 3G/4G, small images, works at 393 px.

## Sources
- [Bagisto Multi-Vendor docs](https://docs.bagisto.com/multi-vendor-marketplace/introduction),
  [Marketplace getting started](https://marketplace-docs.bagisto.com/getting-started/introduction),
  [Split order](https://store.webkul.com/bagisto-marketplace-split-order.html)
- [CS-Cart Multi-Vendor functions](https://www.cs-cart.com/multi-vendor-functions),
  [admin functions](https://www.cs-cart.com/multi-vendor-admin-functions.html),
  [payments and shipments](https://www.cs-cart.com/features-payments-and-shipments.html),
  [Capterra](https://www.capterra.com/p/172642/CS-Cart-Multi-Vendor/reviews/Capterra___1146461/)
- [Lunar on GitHub](https://github.com/lunarphp), [Laravel News on Lunar](https://laravel-news.com/partner/lunar)
- [Jumia: COD 83%](https://digestafrica.com/cash-on-delivery-jumia-mobile),
  [Jumia 14 years in Uganda (Nile Post)](https://nilepost.co.ug/index.php/jumia%20uganda/345689/jumia-basks-in-building-ugandas-e-commerce-ecosystem-in-last-14-years),
  [Card on delivery](https://www.dignited.com/25259/jumia-uganda-adds-card-delivery-list-payment-options)
- [Baymard: reduce cart abandonment](https://baymard.com/blog/reduce-cart-abandonment)
