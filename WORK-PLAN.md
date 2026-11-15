# Kheengz: work plan

Phases build in dependency order. Decisions are in [docs/decisions.md](docs/decisions.md); the research is in
[docs/brainstorm/](docs/brainstorm/README.md).

## Phases

| Phase | What ships | Status |
| --- | --- | --- |
| **0. Foundation** | Workspace; Laravel 13 API and Next.js 16 app from the official installers; core packages installed and published; GiftNest foundation copied (ULIDs, audit trail, enums, serial counters, OTP, queues, Reverb, roles, dashboard shell, standard table, e2e) | done (15 Nov 2026) |
| **1. MVP** | Shop types, brand, homepage sections and `<shop>.kheengz.com` (D19); product video and TikTok links (D20); share sheet, link previews, status cards, QR posters, source-tagged links (D21); shop profile and brand tokens; vendors with their payout number or bank account; categories; products, variants, photos; stock per location; storefront (home, category, search, product, cart, Buy now, one-page checkout); MarzPay and OlyCash (buyer picks) + pay on delivery; order tracking by phone; orders split per vendor; vendor delivery areas and riders; instant payouts with fees taken from the vendor's share (D22); what vendors owe from pay on delivery, and hiding unpaid shops (D14); SMS; admin and vendor dashboards | **in progress**: step 1 of 7 done (16 Nov 2026); see the Phase 1 checklist |
| **2. Grow** | Promo video maker, catalogue feeds, shoppable reels, own domains, resellers, AI listing from a photo (plan-shop-identity-and-marketing.md); coupons, reviews, wishlist, Meilisearch, vendor plans, card payments, returns, WhatsApp order button, pickup points | |
| **3. Mobile and scale** | Expo app (buyer, then vendor); hosted multi-shop (tenancy); suppliers and purchase orders; same product from several vendors | |

## Phase 0 checklist

- [x] Workspace folder, `CLAUDE.md`, `README.md`, `docs/decisions.md`, `.claude/commands` (handoff), code-workspace
- [x] `kheengz-laravel` from `composer create-project laravel/laravel` (13.42.0), MySQL database `kheengz`
- [x] API packages installed with their own installers / `vendor:publish` (Sanctum via `install:api`, Reverb via
      `install:broadcasting`, Spatie permission + activitylog configs and migrations, query-builder, Scramble config,
      kreait Firebase, Boost guidelines + skills; Pest 5, Larastan level 5 with no baseline, Pint). Pint, Larastan and
      the starter tests pass. The permission / activity log migrations are published but **not run**: they get ULID
      keys with the foundation.
- [x] `kheengz-nextjs` from `create-next-app@latest` (16.4.6), web packages installed (PrimeReact held at 10: D11),
      `lib/firebase.ts`, type check clean
- [x] Git: three repos with their GitHub remotes; work on `farouk` (first commits on `main`)
- [x] Firebase dev / prod keys set and validated (D10)
- [x] ~~VAPID keys~~: web push is optional, not in the MVP (D18)
- [ ] `.env.example` additions as modules land (MarzPay and OlyCash keys, SMS)
- [x] **API foundation from GiftNest** (15 Nov 2026): ULID users and permission tables, audit trail with
      visitor origin (D29: DB-IP City Lite), sign-in by email / phone / username, email codes, password
      reset by email and SMS, users, roles (Admin, Staff, Vendor), notifications bell, queues admin, activity
      logs, buyer rate limits, `SerialCounter` from Alpha (never reuses a number), Kheengz-branded emails in
      the Royal colours, 36 routes. Pint clean, Larastan level 5 with 0 errors and no baseline, 35 tests pass.
- [x] **Web foundation from GiftNest** (15 Nov 2026): dashboard shell (Admin, Staff, Vendor), sign-in and reset,
      standard table, Filters, Export (SheetJS 0.20.3 from its CDN), notifications bell, users / roles / queues /
      audit trail screens (with a Where column and the DB-IP credit, D29), profile, a storefront home, Playwright.
      Every page prerendered static with Cache Components; the session read in the browser only; PrimeReact's
      theme in a cascade layer and never on the storefront (D30). Type check, ESLint with no rule switched off,
      `next build` and 13 browser tests pass; no console or hydration errors.
- [x] API docs started from GiftNest's and Alpha's, trimmed to what applies (`kheengz-laravel/docs/README.md`)
- [x] Web docs (`kheengz-nextjs/docs/README.md`: sessions and Cache Components, dashboard patterns, e2e).
      Printing comes with receipts (Phase 1)

**Stop point (reached 15 Nov 2026):** both apps run, sign-in works with Admin seeded, the dashboard shell and an
empty storefront home render, all foundation tests green. Then Phase 1 starts with the data model in plain words
for sign-off.

## Phase 1 checklist

Built in the order of [docs/plan-data-model.md](docs/plan-data-model.md) section 10 (agreed 15 Nov 2026, D31-D35).
Each step ships with its screens, tests and docs before the next starts.

- [x] **1. Vendors and shops** (16 Nov 2026). API: vendors, papers, members, payout destinations (Mobile Money
      checked by MarzPay's name lookup, banks by `bank-transfer/validate`, both against the owner's name), shops
      with their own review, locations, platform settings (`kheengz-laravel/docs/vendors-and-shops.md`).
      Web: Sell on Kheengz (`/sell`), My business, the shop workspace, vendor review, shops list, platform
      settings; the dashboard shell keeps URL reads out of its prerender (`useAppRouter`). Tests: 75 Pest,
      17 Playwright including the whole seller journey.
- [ ] **2. Catalogue**: categories and requests, attributes, units, shop sections; products, variants, price
      breaks, photos
- [ ] **3. Stock** levels and movements
- [ ] **4. Shop look** and homepage sections; the storefront pages
- [ ] **5. Orders**: buyers, addresses, delivery areas, riders; orders, shop orders, items, status changes
- [ ] **6. Money**: shop payment methods, payments, payouts, the shop ledger, debt and hiding
- [ ] **7. Counter sales**; SMS; product video and TikTok links

## Still to settle (bloodykheeng)

Nothing open. Everything is in `docs/decisions.md` (D1-D35).
