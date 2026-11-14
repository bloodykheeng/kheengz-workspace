# The product, in plain words

To agree before any table exists. Each point is a proposal; what we settle moves into `docs/decisions.md`.

## 1. One platform, two editions

- **One codebase.** An installation is a **shop**. A shop always has **vendors** (sellers).
- **Single vendor edition** = a shop with one vendor (the owner, the "house" vendor). Everything vendor-related
  is hidden: no "sold by", no vendor sign-up, no commission screens. This is Fahim's Home Nest on day one.
- **Marketplace edition** = the same shop with vendor sign-up switched on. Sellers apply, the owner approves,
  each seller gets a vendor dashboard. Fahim can switch it on later and nothing migrates.
- **How we sell it:** first as one install per client (their own server or ours, their domain). Later as hosted
  plans where many shops run on one server (database per shop, `stancl/tenancy`). The rule that makes this
  possible later: **nothing about a client is hardcoded**; name, logo, colours, currency, payment keys and mode
  live in the shop's own data or env.

## 2. How a buyer buys (the part that must feel easy)

**Two ways in, one checkout:**
- **Buy now** on every product: opens checkout with just that item. No cart.
- **Add to cart** for several items, then checkout.

**One-page checkout, no login:**
1. Phone number (the identity) and name. Email optional (for a receipt).
2. Delivery: **deliver to me** (area picked from a list, fee shown at once; landmark/directions text) or
   **pick up** (from a pickup point or the vendor's shop).
3. Pay: **Mobile Money** (MTN / Airtel prompt on the phone), **pay on delivery** (cash or Mobile Money to the
   rider), later card.
4. Confirm. Order number shown (`HN-XXXXXXXX` style, from the serial counter), SMS sent.

**After the order:**
- **Track by phone + order number**, with a 6-digit code by SMS to see details (GiftNest's OTP pattern).
- **"Save my details for next time?"** on the confirmation page: confirm by code, now they have an account.
  Next time, phone + code fills everything. **No passwords for buyers** (Shop Pay idea). Google sign-in optional.
- Returning buyers on the same device get their details filled from a cookie they can clear.

**Also:** "Order on WhatsApp" button (the cart as a message to the shop's number) as an optional channel.

**Why:** forced accounts are among the top reasons people abandon checkout (Baymard, ~18%), and Baymard
advises delaying account creation to the confirmation step. In Uganda, pay on delivery built Jumia's trust.

## 3. The objects, in a few sentences

- **Shop profile**: one row for the installation: name, logo, colours, currency, contact phone, WhatsApp,
  edition (single / marketplace).
- **Vendor**: a seller with a shop page (name, logo, banner, about, policies), a status (Pending, Active,
  Suspended), a commission rate (null = the shop's default), and the users who run it (`vendor_users`, Owner /
  Staff, per vendor, like GiftNest's hosts).
- **Category**: a tree (parent). Products sit in one category (plus tags later).
- **Product**: belongs to one vendor; name, slug, description, photos, status (Draft, Active, Archived).
- **Variant**: what is actually bought (size M, colour Blue): SKU, price, compare-at price, weight. A product
  with no options has one variant.
- **Stock**: quantity per variant **per location** (a vendor's shop or store room). MVP: each vendor has one
  location made for them; the table is ready for more.
- **Cart**: belongs to a browser (token) or a buyer; lines of variant + quantity. Prices are read live, never
  frozen in the cart.
- **Order**: what the buyer placed and pays for once: buyer phone/name, delivery choice and fee, totals, payment
  status. Prices **frozen** on its lines at the moment of ordering (master copy rule).
- **Vendor order**: the order split per vendor (one buyer order with items from 3 vendors = 3 vendor orders),
  each with its own fulfilment status (Pending, Confirmed, Packed, Out for delivery, Delivered, Cancelled) and
  its own commission. In single vendor edition there is always exactly one.
- **Payment**: collection attempts (GiftNest's pattern: provider calls only in `app/Payments`, outcomes only
  through one locked, idempotent `PaymentService`). Pay on delivery = a payment recorded when the rider hands
  over the money.
- **Ledger** (`transactions`): a delivered vendor order credits the vendor's sale and debits the commission;
  **payout requests** move the balance to the vendor's Mobile Money or bank (GiftNest's withdrawals).
- **Delivery areas**: area name + fee (Kampala Central, Ntinda, Entebbe...), set by the shop. Pickup points.
- **Coupons**: Phase 2.
- **Suppliers and purchase orders** (where a vendor's stock comes from): Phase 3.

Derived facts are read, never stored: "in stock", a vendor's balance (summed from the ledger), "best seller".

## 4. Roles

- **Admin** (the shop owner's staff): everything.
- **Vendor Owner / Vendor Staff**: per vendor (`vendor_users`), never an account role. Their own products,
  orders, payouts only, enforced by policies on the API.
- **Buyer**: no account needed; an account is just a saved phone.
- **Rider** (Phase 2): sees deliveries assigned to them, marks delivered, records cash collected.

## 5. Phases

| Phase | What ships | Who it serves |
| --- | --- | --- |
| **0. Foundation** | Workspace, Laravel 13 API + Next.js 16 app from official installers, GiftNest foundation (ULIDs, audit trail, OTP, queues, enums, serial counters, dashboard shell, standard table, e2e) | us |
| **1. MVP (Home Nest live)** | Shop profile and brand, categories, products + variants + photos, stock, storefront (home, category, search, product, cart, Buy now, one-page checkout), Mobile Money via OlyCash (works in GiftNest) + pay on delivery, order tracking by phone, admin orders board, SMS, vendors (marketplace switch on), vendor dashboard, commission + ledger + payout requests | Fahim |
| **2. Grow** | Coupons, delivery areas + pickup points + riders, reviews, wishlist, Meilisearch search, vendor plans, Pesapal / Flutterwave (card), returns, WhatsApp order button | Fahim + first paying clients |
| **3. Mobile + scale** | Expo app (buyer first, then vendor), hosted multi-shop (tenancy), suppliers and purchase orders, same product from several vendors | resale |

The web dashboard runs every step the mobile app will, through the same API, so the API is proven first.

## Questions for you to settle

1. **Commission money flow for MVP:** the shop collects all payments and pays vendors out (recommended: simpler
   for buyers, one Mobile Money merchant account, like GiftNest), or each vendor collects and owes commission?
2. **Stock per location from day one** (recommended, cheap now) or a single number on the variant?
3. **Fahim's products:** what does he sell (home goods? furniture? sizes and colours?) This decides which variant
   options and photo layouts matter first.
4. **Delivery:** does Fahim deliver himself (riders), use a courier (SafeBoda, Faras), or pickup only at first?
