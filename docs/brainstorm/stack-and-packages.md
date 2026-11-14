# Stack, packages and workspace

Versions checked 15 Nov 2026. Everything is installed with the official installers at install time
(`composer create-project`, `create-next-app@latest`, `npm install pkg@latest`); published files (migrations,
config) come from the installed version, then only our deliberate changes are re-applied, each with a comment.

## Versions

| Piece | Version now | Note |
| --- | --- | --- |
| Laravel | **13.x** (released 17 Mar 2026; fixes to Sep 2027, security to Mar 2028) | PHP 8.3 to 8.5 |
| PHP | **8.4** (as in Alpha/GiftNest), 8.5 supported | Patch level from your Oct 2026 security note (8.4.27+) |
| Next.js | **16.4.x, at least 16.4.5** | The 3 Nov 2026 security release fixed 7 advisories in 16.4.5 / 15.5.29 |
| React | 19.2 | |
| Expo (Phase 3) | **SDK 58** (15 Oct 2026, React Native 0.88) | New Architecture only |

**Side note on your live apps:** Alpha's `package.json` pins `next` **16.4.2**, below the 16.4.5 security
fix. GiftNest and Elevate Pesa admin are worth checking too.

## Laravel API

Our usual stack (Alpha / GiftNest), plus what a shop needs:

| Package | Why |
| --- | --- |
| `laravel/sanctum` | SPA cookie auth for the web, tokens for the mobile app |
| `laravel/reverb` + `laravel-echo` | Live order board, stock and payment status |
| `spatie/laravel-permission` | Roles and permissions (Admin; vendor roles are per vendor in `vendor_users`) |
| `spatie/laravel-activitylog` | Audit trail through `snapshotSchema()` |
| `spatie/laravel-query-builder` | Filters, sort, `include=` on lists |
| `dedoc/scramble` | API docs (the mobile developer reads these) |
| `mpdf/mpdf` | Invoices, receipts, packing slips |
| `laravel/scout` + **Meilisearch** (Phase 2) | Product search with facets and typo tolerance. Meilisearch has the official Scout driver; Typesense's is community-run. MVP: Scout's `database` driver |
| `laravel/horizon` + Redis (production) | Order, SMS and payment jobs need a reliable queue; dev stays `database` |
| `pestphp/pest`, `larastan/larastan`, `laravel/pint`, `laravel/boost`, `laravel/pail` | Tests, level-5 analysis, format, AI guidelines, logs |
| `stancl/tenancy` (Phase 3) | Hosted multi-shop. v3.10 supports Laravel 13; v4 has no stable tag yet |

Not used, and why: **Lunar / Bagisto as a base** (bigint ids, JSON attribute columns, their admin);
**spatie/laravel-medialibrary** (JSON columns on `media`; our `product_photos` table plus Next `<Image>` as in
GiftNest); **a cart package** (the cart is our own table; prices are read live).

## Payments (`app/Payments`, one gateway class each)

| Provider | Use | Phase |
| --- | --- | --- |
| **OlyCash** | MTN / Airtel prompts; already proven in GiftNest | 1 |
| **Pay on delivery** | Not a gateway: a payment recorded when the rider hands over the money | 1 |
| **Pesapal** (API v3) | One integration: MTN, Airtel, cards, banks; ~2.5% | 2 |
| **Flutterwave** | Uganda mobile money + cards; good for clients in other countries | 2 |
| Direct MTN MoMo / Airtel APIs | Cheaper per transaction at volume, weeks of onboarding | later, per client |

A client picks their providers in env; adding one = a class implementing `PaymentGateway` plus one line in the
manager (GiftNest's design).

## Next.js web app

One app, three faces (Alpha's route groups):

- `app/(shop)/` **storefront**: home, category, search, product, cart, checkout, track order, vendor pages.
  Server-rendered for SEO and speed, our own Tailwind components (`sf-*`, like GiftNest's `gn-*`), themed by the
  shop's brand tokens. No PrimeReact here: it keeps the buyer's pages light on data.
- `app/dashboard/@admin/` and `app/dashboard/@vendor/`: PrimeReact 10 + Tailwind 4, the standard table,
  filters, export, print (our dashboard patterns).
- `app/(auth)/`.

Packages: TanStack Query 5, react-hook-form + zod 4, axios (`services/axiosApi.ts` + `services/<domain>`),
laravel-echo + pusher-js, Highcharts, motion (Framer Motion), react-to-print, react-international-phone,
next-auth (Google), Playwright, `nextjs-toploader`. Fonts through `next/font`.

## Workspace (as GiftNest)

```
<name>-workspace/            # own repo: CLAUDE.md, README, WORK-PLAN.md, docs/decisions.md, .claude/handoffs
  <name>-laravel/            # API repo
  <name>-nextjs/             # web repo (storefront + dashboards)
  <name>-mobile/             # Phase 3, Expo
  <name>.code-workspace
```

Branch `farouk`, commit only when asked, each app its own repo. The foundation is copied from GiftNest (the
newest), and anything found broken while copying gets a `notes-for-giftnest/` note, never a change there.

## Sources
- [Laravel 13 release notes](https://laravel.com/docs/13.x/releases.md), [endoflife.date Laravel](https://www.endoflife.date/laravel)
- [Next.js blog](https://nextjs.org/blog), [November 2026 security release notice](https://nextjs.org/blog/upcoming-nextjs-security-release-november-2026),
  [write-up: 16.4.5 / 15.5.29](https://www.buildmvpfast.com/blog/nextjs-november-2026-security-release-cache-leak)
- [Expo SDK 58 changelog](https://expo.dev/changelog/sdk-58.md)
- [stancl/tenancy on Packagist](https://packagist.org/packages/stancl/tenancy), [Tenancy v4 upgrade guide](https://v4.tenancyforlaravel.com/upgrading)
- [Scout in 2026: Meilisearch vs Typesense vs Algolia](https://dev.to/hafiz619/laravel-scout-in-2026-meilisearch-vs-typesense-vs-algolia-vs-database-driver-2omi)
- [Flutterwave Uganda docs](https://developer.flutterwave.com/docs/uganda), [Pesapal API v3](https://www.pesapal.com/blog/why-developers-in-kenya-uganda-and-beyond-are-choosing-pesapal-s-apis),
  [Flutterwave vs EasyPay vs MoMo API](https://mctaba.com/learn/uganda/flutterwave-vs-easypay-vs-momo-api-uganda) (vendor blog; fees unverified)
