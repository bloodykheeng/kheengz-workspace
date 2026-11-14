# Kheengz: work plan

Phases build in dependency order. Decisions are in [docs/decisions.md](docs/decisions.md); the research is in
[docs/brainstorm/](docs/brainstorm/README.md).

## Phases

| Phase | What ships | Status |
| --- | --- | --- |
| **0. Foundation** | Workspace; Laravel 13 API and Next.js 16 app from the official installers; core packages installed and published; GiftNest foundation copied (ULIDs, audit trail, enums, serial counters, OTP, queues, Reverb, roles, dashboard shell, standard table, e2e) | **in progress** |
| **1. MVP** | Shop profile and brand tokens; vendors (+ connect MarzPay); categories; products, variants, photos; stock per location; storefront (home, category, search, product, cart, Buy now, one-page checkout); MarzPay Mobile Money + pay on delivery; order tracking by phone; orders split per vendor; vendor delivery areas and riders; commission ledger and paying it; SMS; admin and vendor dashboards | next |
| **2. Grow** | Coupons, reviews, wishlist, Meilisearch, vendor plans, card payments, returns, WhatsApp order button, pickup points | |
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
- [x] Git: three repos on `main` with their GitHub remotes (nothing committed yet)
- [x] Firebase dev / prod keys set and validated (D10)
- [x] ~~VAPID keys~~: web push is optional, not in the MVP (D18)
- [ ] `.env.example` additions as modules land (MarzPay and OlyCash keys, SMS)
- [ ] GiftNest foundation copied and renamed, with its tests passing here
- [ ] Docs per app (`docs/*.md`) started from GiftNest's, renamed and trimmed to what applies

**Stop point:** both apps run, sign-in works with Admin seeded, the dashboard shell and an empty storefront home
render, all foundation tests green. Then Phase 1 starts with the data model in plain words for sign-off.

## Still to settle (bloodykheeng)

1. **Who carries the gateway fee** (MarzPay 3% / card 5%): taken from the vendor's payout (my view) or a buyer
   service fee? (D13)
2. **When payouts go out:** automatically once a day from Delivered orders (my view), or straight after each
   payment? (D12)
3. The exact commission percentage, which service fees, and the unpaid-debt limit before products hide (D13, D14).
4. Walk-in sales recorded through a counter sale screen (D17)?
