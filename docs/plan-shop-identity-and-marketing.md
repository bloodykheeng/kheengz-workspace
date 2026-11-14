# Plan: every shop looks like itself, sells with video, and markets itself

Started 15 Nov 2026 from bloodykheeng: "the shops should be customizable so that they all don't look the same ...
allow video uploads of products, also marketing sharing to TikTok etc ... think outside the box ... a hardware shop
shouldn't be like a vegetable shop". Decisions D19 to D21 settle **that** we do it; this file proposes **how**.
Visual: the "Shop types" section of https://claude.ai/artifact/42W2wUoNb5fPoWPtgH2ivx

## 1. Why this is our edge

- Jumia and Kilimall: every seller looks the same; the seller is a name under the price.
- Shopify: shops differ, but through paid themes, apps and a designer, and it is card-first.
- WhatsApp and TikTok sellers: strong identity and video, but no checkout, no stock, orders typed by hand.
- **Kheengz:** a shop with its own look and address, video-first products, sharing that brings buyers back to a
  real checkout with Mobile Money or pay on delivery. Social commerce in Africa runs on WhatsApp and short video;
  sellers there still work from static catalogues and hand-typed orders (sources at the bottom).

## 2. Shop types: a hardware shop is not a vegetable shop

A **shop type** changes how products are described, sold and laid out, not only the colours. The vendor picks one
when opening the shop (changeable later). Each type brings defaults the vendor can then adjust.

| | Household & plastics (Home Nest) | Hardware & building | Fresh produce & groceries | Fashion & beauty | Electronics & phones |
| --- | --- | --- | --- | --- | --- |
| **Describing a product** | colour, size (litres, cm), material | size, gauge, length, material, brand, grade | variety, origin, freshness date | size, colour, fit, fabric | brand, model, storage, warranty |
| **Sold by** | piece, dozen, bale | piece, metre, bag, kg, roll, sheet | kg, bunch, heap, tray, crate | piece | piece |
| **Listing looks like** | photo grid, colour dots | **dense list**: name, spec line, unit price, stock; filters by spec | **big photos**, price per kg, quick + / − on the card | tall photos, swatches, video first | spec cards, compare |
| **Product page leads with** | colours, wholesale price breaks | spec table, quantity in units, **request a quote** for bulk | today's price, weight picker, substitutions allowed | size guide, video, looks | specs, warranty, what's in the box |
| **Checkout extras** | dozen / bale prices | delivery by truck or boda by weight; quote → order | **delivery slot** (today morning / evening), minimum order | returns line | warranty card |
| **Homepage starts with** | shop by room, best sellers | shop by project (roofing, plumbing), brands | today's prices, fresh today, reorder last basket | new in, lookbook, reels | deals, compare, brands |
| **Default palette** | Royal | slate and safety orange | leaf green and tomato | Busuuti | Cobalt Market |

A **General** type covers anything else. New types are added by us in code (each has real behaviour), not typed
in by vendors.

**In plain words (to agree before any table):**
- Shop type: a fixed list in code (an enum) choosing layout and behaviour and the defaults below.
- **Attributes** (Gauge, Length, Colour, Variety) are rows grouped into **attribute sets** per category; a product's
  values are rows, so filters and reports can count them (no JSON).
- **Units of sale** (piece, dozen, kg, metre, bag) are rows; a variant has one unit and quantity price breaks (D17).
  Weighed items take decimals (1.5 kg), counted ones don't.
- A vendor's **homepage sections** are rows in order (type, title, the category or products it shows, layout).

## 3. Customising a shop (choose and arrange, never break)

| Level | What the vendor changes |
| --- | --- |
| **Brand** | logo, banner (photo or video), primary and accent colour (contrast checked on save), font pair from a short list, corner style, a tagline |
| **Template** | the shop type's layout, with a few variants (grid density, card style) |
| **Homepage** | sections added, removed and dragged in order: hero, categories, product grid, video reels, promo banner with countdown, reviews, our story, map and opening hours, WhatsApp button, FAQ |
| **Address** | `homenest.kheengz.com` from day one; their own domain (`homenest.ug`) later, with free HTTPS |
| **Marketplace page** | inside the Kheengz marketplace the vendor's page uses their brand; the marketplace's own pages keep the Kheengz look |

**Guardrails** (what keeps every shop fast and trustworthy): themes and sections are built by us, so no custom code
or scripts; checkout, prices, trust lines (pay on delivery, delivery fee, returns) and buttons keep their places;
image and video sizes are enforced; a preview before publishing; one click back to the default.

## 4. Product video

- **Upload:** up to 4 videos per product, vertical (9:16) or wide, **up to 300 MB each, any length** (D25),
  MP4 / MOV / WebM, from the phone's gallery or camera.
- **Processing (queue job, ffmpeg):** keep the original; make a 720p and a 480p H.264 MP4 and a poster image;
  never served until ready ("Processing" shows on the product meanwhile). Files in object storage (e.g. Cloudflare
  R2, no fees for bandwidth), not the server's disk.
- **Playback, light on data:** the poster shows first, video plays on tap; the reels strip plays muted only while on
  screen and never when the phone asks to save data; 480p unless the connection is good.
- **Bring your TikToks:** a vendor pastes a TikTok link and it shows on the product with TikTok's embed player,
  loaded only when tapped (TikTok's script is heavy). Zero storage cost, and vendors already post there.
- **Shoppable reels:** a shop's videos as a swipeable strip; each video tagged with the products in it, a tap opens
  the product with Buy now.
- **Later, when volume asks for it:** move delivery to a streaming service (Cloudflare Stream about $5 per 1,000
  minutes stored and $1 per 1,000 delivered; Bunny Stream usually cheaper at volume) for adaptive quality.

## 5. Marketing and sharing

- **Share sheet on every product and shop:** WhatsApp first, then TikTok, Facebook, Instagram, X, copy link; on
  phones the system share sheet (any installed app).
- **Link previews that sell:** each product's link shows a card with photo, price and shop name when pasted in
  WhatsApp or Facebook (an image generated per product; GiftNest's link-previews pattern).
- **Status and story cards:** one tap makes a 9:16 image with the photo, price, shop name and a short link / QR,
  ready for WhatsApp Status, TikTok and Reels.
- **Promo video maker:** pick products, get a 9:16 MP4 slideshow (photos, prices, shop name, short link) made by
  ffmpeg, to post on TikTok or Status. Vendors without editing skills get a video in a minute.
- **Links that know where buyers came from:** every shared link carries its source; the vendor sees "orders from
  TikTok: 12, WhatsApp: 30".
- **QR posters** for the physical shop (Fahim's walk-ins scan it to follow the shop and order again) and on receipts.
- **Catalogue feeds:** each shop's products as a feed for Facebook / Instagram / WhatsApp catalogues and Google's
  free shopping listings.
- **Promotions:** flash sales with a countdown, coupons, bundles ("basin + bucket"), SMS to past buyers who agreed.
- **Resellers:** anyone shares a product with their own link and earns a cut of the sale. This is how many people
  already sell other shops' goods on WhatsApp Status.
- **Posting straight to TikTok** from the dashboard: only once TikTok audits our app; until then TikTok allows
  private posts only and 5 users a day (TikTok Content Posting API). Sharing and the promo video cover it until then.

## 6. More outside-the-box ideas (proposals)

- **List a product from a photo:** the vendor snaps it; AI suggests the name, description, category and attributes,
  and cleans the background. The vendor checks and publishes.
- **Order from a list:** a buyer types or photographs a shopping list (hardware, groceries); the shop sends back a
  quote that becomes an order in one tap.
- **Today's prices board** for produce: the vendor updates all prices in one screen each morning.
- **Reorder in one tap** from a past order; **"open now"** with opening hours and a map to the shop.
- **Reviews with photos and video** from buyers who received the item.
- **Storefront in Luganda and Swahili** as well as English.

## 7. Phasing (proposed)

| Phase | Shop identity | Video | Marketing |
| --- | --- | --- | --- |
| **1 (MVP)** | Shop types (D24): Household & home, Electronics & phones, Groceries & fresh produce, Fashion & beauty, Hardware & building, General. Brand, homepage sections, `<shop>.kheengz.com` | Upload + processing + poster; TikTok links | Share sheet, link previews, status card image, QR poster, source-tagged links |
| **2** | Template variants, own domains | Shoppable reels with product tags | Promo video maker, catalogue feeds, flash sales, coupons, bundles, resellers, AI listing from a photo |
| **3** | More types as clients ask | Streaming service | Direct posting to TikTok (after audit), order from a list |

## Questions

Settled 15 Nov 2026: first shop types (D24), video limits (D25), `<shop>.kheengz.com` in the marketplace and no
subdomain in the single vendor edition (D26).

## Sources

- [TikTok Content Posting API: Direct Post](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post) (unaudited clients: private only)
- [TikTok Embed Videos](https://developers.tiktok.com/docs/en/embed-videos) (oEmbed; Embed Player recommended for new integrations)
- [Mux vs Cloudflare Stream vs Bunny Stream cost](https://apicostcalc.com/mux-vs-cloudflare-stream-vs-bunny-stream-cost-calculator.html), [FastPix comparison](https://www.fastpix.com/blog/5-best-cloudflare-stream-alternatives-in-2026) (third-party; confirm on vendors' pages)
- [Social commerce in Africa (Daba)](https://dabafinance.com/en/learn/blogs/social-commerce-in-africa-the-27-97-billion-opportunity), [Is social commerce the next big thing in Africa? (How We Made It In Africa)](https://www.howwemadeitinafrica.com/is-social-commerce-the-next-big-thing-for-retail-in-africa/182685/), [Shopify: social commerce platforms](https://www.shopify.com/id/enterprise/blog/social-commerce-platforms)
