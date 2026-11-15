# Phase 1 data model, in plain words (for sign-off)

What Kheengz keeps a record of in Phase 1, what each record holds, and how they connect. Nothing here
is built yet: once you agree, each part becomes tables, in the order at the bottom. Decisions are cited
(D-numbers in `decisions.md`); D31 to D35 were settled on 15 Nov 2026 from your answers and the research
at the end.

House rules for every record (GiftNest's, CLAUDE.md): money in whole shillings, times stored in UTC and
days counted in Kampala, phones as digits with 256, every change audited with who and from where, nothing
countable hidden in a JSON blob, and a number people read (an order number) never reused.

## 1. Vendors and their shops (D31)

A **vendor** is the person or business we verify once. A vendor can run **many shops**, and **each shop is
approved on its own** before it can sell, so nobody opens a second shop to get round a ban or a debt.

| Record | Holds | Notes |
| --- | --- | --- |
| **Vendor** | legal or business name, contact phone and email, the owner's ID (national ID number, a photo of the card), business registration number if a company, status (Pending, Approved, Suspended), who approved and when | Approved once by the Admin (D35). Suspending a vendor closes all their shops |
| **Shop** | the vendor, name, address name (`homenest` → `homenest.kheengz.com`), shop type (D24), description, contact phone, status (Draft, Pending review, Active, Suspended, Closed), who approved and when, commission rate and debt limits if different from the default (D23, D28) | Each new shop goes to the Admin for review. The single vendor edition has one vendor with one shop (D2, D26) |
| **Shop review** | each decision on a shop (approved, asked for changes, refused, suspended), the reason, who, when | So the history of every shop is plain |
| **Vendor document** | ID card photos, business certificate, what each is, checked or not | Kept private: only the Admin sees them |
| **Vendor member** | which accounts work for the vendor, as **Owner** or **Staff**, and **which of its shops** each Staff member may run (D26) | An Owner runs every shop of that vendor |
| **Payout destination** | Mobile Money number and network, or bank and account number, the name the network or bank returned when we checked it | Belongs to the vendor; each shop chooses one. A destination whose name does not match the vendor's verified name waits for the Admin (a common scam sign). Every change is audited and SMSed to the Owner (D12) |
| **Location** | the shop, name (shop, store room), address, area, phone, opening hours | Each shop gets one when created (D5) |
| **Delivery area** | the shop, area, fee, usual time, and which payment methods that area allows (pay on delivery on or off, a top order value for it) | Per shop (D7, D34) |
| **Rider** | the vendor, name, phone, plate | Saved once and used by any of the vendor's shops; no rider accounts yet (D7) |

How shops stay honest:
- **Only the shop that owes is hidden**, like a landlord who locks one stall in an arcade, not all three.
  Each shop has its own ledger and its own limits (D28); the vendor's other shops keep selling.
- **The Admin sees everything together**: what each shop owes and the vendor's total, on the vendor's page.
  When the vendor asks for a new shop, the review shows what their other shops owe, and the Admin decides.
- **Each shop page says who runs it**: "Run by Home Nest Ltd, verified since Nov 2026", with their other
  shops listed (as Etsy requires).
- **No copied shops**: a shop that only repeats another shop's products is refused at review.

## 2. What a shop looks like (D19)

| Record | Holds |
| --- | --- |
| **Shop look** (part of the shop) | logo, banner photo or video, primary and accent colour, font pair from a short list, corner style, tagline, layout variant |
| **Homepage section** | its kind (hero, shop sections, products, video reels, promo banner, reviews, our story, map and hours, WhatsApp button, FAQ), title, position, on or off, and what it shows (a shop section, a chosen list of products, a countdown end) |

The kinds are a fixed list in code, so no vendor can add code or scripts.

## 3. Catalogue: two kinds of grouping (D32)

Every big marketplace keeps two separate things, and so do we:

| | **Marketplace category** | **Shop section** |
| --- | --- | --- |
| What it is | Kheengz's own tree: Household › Kitchen › Basins | The shop's own groups, named however the vendor likes: "Back to school", "Wedding sets", "Under 10k" |
| Who makes it | The Admin (vendors ask for a missing one from their product form; the Admin adds it, often the same day) | Each shop, freely |
| Every product has | exactly one, the most specific that fits | none, one or several |
| Used for | marketplace browsing and search, filters (the attributes below), reports, Google and Facebook catalogue feeds | the shop's menu, its homepage sections, its own links |

This way a vendor organises their shop any way they want ("you cannot know what someone has"), and a buyer
still finds basins from every shop under Basins, with the same filters.

| Record | Holds | Notes |
| --- | --- | --- |
| **Category** | name, parent, icon, position, on or off | The Admin's tree |
| **Category request** | the shop, the name asked for, where it should sit, status, the Admin's answer | Until it is added, the product goes in the nearest category, and the vendor is told when theirs exists |
| **Attribute** | what describes products in a category, for filters (Colour, Litres, Gauge, Length, Variety, Brand) and how it is entered (a list, a number with a unit, text) | Set per category by the Admin (plan §2) |
| **Attribute option** | the choices (Red, Blue; 28, 30 gauge) | Counted by filters and reports |
| **Shop section** | the shop, name, position, on or off, its photo | |
| **Unit of sale** | piece, dozen, bale, kg, metre, bag, bunch; whether it takes decimals (1.5 kg) | A starting list per shop type; the Admin adds more |
| **Product** | shop, category, shop sections, name, description, address name for links, status (Draft, Active, Hidden), delivered or collected only | |
| **Variant option** | an option a vendor sets on their own product (Size, Colour, Scent) and its values | A vendor is never limited to the category's attributes; when an option matches one (Colour), it also feeds the filter |
| **Variant** | what the buyer picks (Red 20 L basin): its option values, SKU, barcode, unit, price, was-price, weight | At least one per product |
| **Price break** | from what quantity a lower price applies (12 or more: UGX 4,500 each) | Wholesale (D17) |
| **Product photo** | file, order, which variant it shows | Upload or a pasted link copied to our storage |
| **Product video** | original, the light version, poster, length, size, order | Up to 4, 300 MB each, resumable (D20, D25) |
| **TikTok link** | the TikTok video shown on the product | D20 |

## 4. Stock (D5, D33)

| Record | Holds |
| --- | --- |
| **Stock level** | how many of a variant are at a location now, and how many are held for orders being paid or not yet dispatched |
| **Stock movement** | every change and its reason (received, held for an order, released, sold online, counter sale, returned, counted, damaged), quantity, who, and the order or sale it came from |

Stock is **held the moment an order is placed** and released if payment fails, the order is cancelled, or an
online payment is not completed in time (D33). The level is kept as a figure so checkout can lock and check it
in one step; every movement is a row, so it can always be explained and recounted. "In stock" on the
storefront is the sum over the shop's locations.

## 5. Buyers, orders and delivery

| Record | Holds | Notes |
| --- | --- | --- |
| **Buyer** | phone (the identity), name, email if given, first and last seen | No password; an account is offered after the order with a code |
| **Buyer address** | area, street or landmark, directions, map pin if given, a label (Home, Shop) | So the next order is two taps |
| **Order** | number (KZ…), buyer, the address as it was, totals, status, and where the buyer came from: IP, country, region, city, device, browser, share-link source, referrer (D29) | One per checkout, one number for the buyer |
| **Shop order** | the part one shop fulfils: shop, location it ships from, delivery area and fee, **how this part is paid** (D34), status, rider, the time of each step | One per shop in the order |
| **Order item** | variant, the product name, variant and unit as they were, quantity, unit price, line total | Never rewritten by a later price change |
| **Status change** | each step of a shop order (Placed, Confirmed, Packed, Out for delivery, Delivered, Cancelled, Returned), who and when | The buyer's tracking page reads these |

The cart lives **in the browser** (D33): no table in Phase 1; a server cart for reminder SMS comes in Phase 2.
Tracking needs the phone number and the order number together.

## 6. Paying: every available method, shown before checkout (D34)

**The Admin** switches gateways on for the whole platform (MarzPay, OlyCash; card through MarzPay). **Each
shop** switches on what it accepts: Mobile Money and card through our gateways, and pay on delivery, which it
can limit by delivery area and by order value (D7). Nothing a shop allows is hidden from the buyer.

What the buyer sees:
- **On the product page**: "Pay on delivery available in Kampala" (or not), for the area they last used, so
  nobody learns it only at the end.
- **In the cart, grouped by shop**: each shop's delivery fee, delivery time and the methods it takes.
- **At checkout**: if every shop takes the same method, one choice for everything. If they differ, each shop's
  group gets its own choice. One cart cannot be both paid and unpaid, so the online part is paid with **one**
  Mobile Money prompt for its total, and the rest is paid to each shop's rider. The summary says it plainly
  before the buyer confirms: "Pay now UGX 120,000 (Tech Hub) · Pay the rider UGX 50,000 (Home Nest)".
- **One order number** for all of it, one confirmation SMS, then one SMS per shop as each is on its way.

| Record | Holds | Notes |
| --- | --- | --- |
| **Shop payment method** | the shop, the method, on or off | Delivery areas carry the pay-on-delivery limits (section 1) |
| **Payment** | the order and which shop orders it covers, gateway, the number charged, amount, gateway fee, our reference and theirs, status (Pending, Successful, Failed), the provider's answer for support | One per attempt; outcomes only through the locked, idempotent payment service (GiftNest, D15) |
| **Payout** | the shop order, the destination as it was, amount, payout fee, gateway, status (Queued, Sent, Failed, Paid), attempts, references | Sent as soon as the payment succeeds, never twice (D22) |
| **Shop ledger entry** | one line of a shop's money: sale, gateway fee, commission, service fee, payout fee, payout, commission owed on a pay-on-delivery sale, refund, debt paid, adjustment; in or out; what it came from | What a shop is owed, or owes, is the sum of these, never a stored figure. The payout statement prints them |
| **Debt payment** | a vendor paying what one shop owes, by Mobile Money prompt from the dashboard | Paying it shows that shop again at once (D14, D28) |
| **Platform settings** | default commission %, fixed fee per shop order, service fee (UGX 0 at launch), the two debt limits, which gateways are on | Admin only; each change audited |

Each gateway's wallet balance is compared with what is due to shops on the Admin dashboard (D12).

## 7. Counter sales (D27)

| Record | Holds |
| --- | --- |
| **Counter sale** | number (CS…), shop, location, who sold it, how it was paid (cash, Mobile Money to their own number, or a prompt through our gateway), total, the buyer's phone if given for an SMS receipt |
| **Counter sale item** | variant, quantity, price, line total, as they were |

Stock comes off that location; no commission; a prompt through our gateway still carries its fee.

## 8. Messages

| Record | Holds |
| --- | --- |
| **SMS** | to, text, why (order placed, rider on the way, payout sent, debt warning, shop approved), status, cost, the record it was about |

In-app notices use the bell built in Phase 0.

## 9. What waits for later phases

Reviews, coupons, wishlists, a server cart and reminder SMS, returns with refunds through the gateway, rider
accounts, vendor plans with a monthly fee, own domains, suppliers and purchase orders, the same product from
several shops (Phase 2 and 3, WORK-PLAN).

## 10. Order of building, once agreed

1. Vendors, documents, shops and their review, members, payout destinations, locations; platform settings
2. Categories and requests, attributes, units, shop sections; products, variants, price breaks, photos
3. Stock levels and movements
4. Shop look and homepage sections; the storefront pages
5. Buyers, addresses, delivery areas, riders; orders, shop orders, items, status changes
6. Shop payment methods, payments, payouts, the shop ledger, debt and hiding
7. Counter sales; SMS; product video and TikTok links

Each step ships with its screens, its tests and its doc before the next starts.

## Research behind D31 to D34

- **Many shops, each judged on its own:** Etsy lets one seller run several shops; each must follow the seller
  policy on its own, the others must be disclosed, and duplicate shops are banned
  ([Listadum](https://www.listadum.com/blog/can-you-have-more-than-one-etsy-shop),
  [Etsy Community](https://community.etsy.com/t5/Technical-Issues/Multiple-Shops/td-p/145421315)).
  Fraud guides: verify the owner, tie every store to that owner and payout account, and treat a payout account
  in another name as a warning sign ([Shipturtle](https://www.shipturtle.com/blog/verify-sellers-fraud-prevention-marketplace-shipturtle),
  [iDenfy](https://idenfy.com/glossary/know-your-seller-kys/), [Nuvei](https://www.nuvei.com/posts/marketplace-fraud-seller-onboarding-trust)).
- **Categories and the shop's own groups:** Shopify keeps a fixed category list for search and channels and
  lets merchants make collections for their storefront
  ([Shogun](https://getshogun.com/learn/collections-vs-categories-shopify),
  [Charle](https://www.charleagency.com/articles/product-taxonomy/)); Etsy's sellers make up to 20 shop sections
  of their own ([Etsy Help](https://help.etsy.com/hc/en-gb/articles/360000345048-How-to-Create-and-Manage-Shop-Sections));
  Jumia's sellers pick the most specific category from Jumia's list ([Jumia Vendor Center](https://vendor.jumia.com/?p=2194)).
- **Payment in a cart from several sellers:** "One cart cannot be paid and unpaid at the same time, so either
  cash on delivery drops out of a mixed cart, or the cart splits before payment"; keep one order number, and say
  it in the summary rather than after ([Mercur, checkout](https://www.mercurjs.com/marketplace-academy/marketplace-checkout-several-sellers),
  [Mercur, cart](https://www.mercurjs.com/marketplace-academy/multi-vendor-shopping-cart-marketplace)). Jumia shows
  pay on delivery by product, seller and area at checkout ([Jumia](https://www.jumia.com.ng/slp/cod-options)).
  We split before payment, per shop, so no method a shop allows is taken away.
