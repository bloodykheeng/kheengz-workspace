# Design direction

Visual version (three palettes drawn as phone storefronts, plus the checkout): the published artifact linked in
`README.md`. This file is the written record.

## Two layers of brand

1. **The platform's look** (our marketing site, sign-in, dashboards): one fixed identity.
2. **Each shop's look** (the storefront buyers see): set by the shop owner from the dashboard as **tokens**
   (primary colour, accent, logo, banner, rounded or sharp corners, font pick from a short list). Stored on the
   shop profile, written into CSS variables by the server so the page never flashes the wrong colours (as
   GiftNest's `--ev-*` event themes). Contrast is checked when they save, so no shop can pick unreadable text.

## Three directions for the platform

| | **Royal** (recommended) | **Cobalt Market** | **Busuuti** |
| --- | --- | --- | --- |
| Idea | The kheeng: emerald and gold. Money, trust, status | Clean, bright, everyday market | The gomesi's silk: confident magenta with deep teal |
| Primary | Emerald `#0E5E45` | Cobalt `#2348D8` | Magenta `#B0175E` |
| Accent | Gold `#D9A21B` | Sunflower `#F7C531` | Teal `#0F5C63` |
| Feel | Premium without being cold | Fast, friendly, discount-store energy | Fashion, beauty, bold |

Why Royal: it ties straight to the name, green reads as "money is safe" next to a Mobile Money prompt, and gold
gives one strong place for the call to action. The other two make good **preset storefront themes** for clients.

## Type

- Headings: **Bricolage Grotesque** (characterful, still legible at small sizes on a phone).
- Body and UI: **Geist** (Next.js's own family, through `next/font`, no layout shift).
- Prices and order numbers: tabular figures, so columns line up.

## Storefront rules

- **Mobile first, checked at 393 px.** Sticky bottom bar on the product page: price, Buy now, Add to cart.
- **Buy now is the strong button; Add to cart the quiet one.** One strong button per screen.
- **Light pages:** server-rendered, `<Image sizes>` with small widths, no dashboard UI kit on buyer pages, no
  autoplay video. Most buyers are on mobile data.
- **Trust in sight on the product page:** "Pay on delivery available", delivery fee for their area, the vendor's
  name and rating (marketplace edition), returns policy in one line.
- **Motion only where it reports something:** the cart count ticking up, the Mobile Money "check your phone"
  screen. No decorative animation.
- **Loading in the shape of what is coming** and never a blank page (our standing rule).
- Dark mode on the storefront follows the device; the shop's brand tokens get dark variants automatically.

## Dashboards

PrimeReact 10 + Tailwind 4 with the platform's tokens, our standard table, Filters dialog, Export, skeletons,
confirm on every write. The order board is the main screen: new orders arrive live (Reverb) with a sound.

## Sources (directional, vendor blogs)
- [Redefine Web: 2026 DTC design trends](https://redefineweb.com/blog/ecommerce-web-design-trends-2026-that-drive-conversions/)
- [GemPages: 2026 ecommerce design trends](https://gempages.net/blogs/shopify/ecommerce-design-trends)
- [HTMLBurger: ecommerce trends 2026](https://htmlburger.com/blog/ecommerce-trends-2026/)
- [Baymard: reduce cart abandonment](https://baymard.com/blog/reduce-cart-abandonment)
