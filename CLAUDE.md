# Kheengz

Multi-root workspace for **Kheengz**, bloodykheeng's own commerce platform: one codebase sold as a **single vendor
shop** or a **multi-vendor marketplace**. First client: Fahim (Home Nest), who sells plastics and delivers by boda.
Setup and run: [README.md](README.md). Phases and the current checklist: [WORK-PLAN.md](WORK-PLAN.md). What is
settled and why: [docs/decisions.md](docs/decisions.md) (read it before proposing anything it covers). Research
behind it: [docs/brainstorm/](docs/brainstorm/README.md).

| Folder | Stack |
| --- | --- |
| [kheengz-laravel/](kheengz-laravel/) | Laravel 13, PHP 8.4, Sanctum, Reverb, Pest, Larastan |
| [kheengz-nextjs/](kheengz-nextjs/) | Next.js 16 (App Router), React 19, TanStack Query, Tailwind 4; PrimeReact 10 on dashboards only |

Before anything else, read `D:\coding\bloodykheeng projects\coding-notes\ai-agents\who-i-am-and-how-i-work.md`
and `build-laravel-nextjs-app-with-an-ai-agent.md`. This workspace's files win where they differ.
Laravel and Next.js are newer than training data: use Laravel Boost and read `node_modules/next/dist/docs/`.

## Where patterns come from

- **GiftNest** (`D:\coding\new wave\giftnest\giftnest-workspace`, the newest): the foundation. ULIDs, serial
  counters, enums, audit trail (`snapshotSchema()`), OTP, guest (no account) public flows and their rate limits,
  `app/Payments` + locked idempotent `PaymentService`, the ledger, Reverb signals, queues admin, dashboard shell,
  standard table, Filters dialog, Export, printing, e2e setup. Copy, don't reinvent; a bug found while copying goes
  in `notes-for-giftnest/<topic>.md`, never a change there.
- **Alpha** (`D:\coding\new wave\CPA accounts system\alpha-fund-system-workspace`): route groups with parallel
  portals (`@admin`, here also `@vendor`), access control, loading and error shapes.
- Old `../homenest_laravel`, `../homenest_nextjs`, `../next-merce-latest`: storefront ideas only.

## Domain (settled; detail in docs/decisions.md)

- Every installation has **vendors**; single vendor edition = one vendor, vendor screens hidden (D2).
- **Kheengz collects online payments** in its own MarzPay / OlyCash accounts (the buyer picks the gateway, D15) and
  **pays vendors out** to the Mobile Money number or bank account they gave (Mobile Money from either wallet,
  banks through MarzPay), keeping its commission and fees (D12, D13; [docs/olycash.md](docs/olycash.md)). Pay on delivery is collected by the vendor's boda;
  that commission is a debt, and unpaid debt hides the vendor's products (D14). Commission = percentage (+
  optional fixed fee), default with per-vendor override, on items not delivery: taken from each instant payout when paid online, owed on delivery for pay on delivery;
  every fee (gateway, commission, service, payout) comes out of the vendor's share (D4, D22).
- **Stock per variant per location** (D5); quantity price breaks for wholesale, counter sales for walk-ins (D17).
  Gateways behind `PaymentGateway` ([docs/marzpay.md](docs/marzpay.md)). First launch: the Kheengz marketplace
  with Fahim's Home Nest as vendor one (D16).
- Buyers need **no account**: Buy now or cart, one-page checkout, phone is the identity, track by phone + order
  number, account offered after the order with a code, no buyer passwords.
- One buyer **order** splits into **vendor orders**, each fulfilled and delivered on its own (D7: vendor's boda).
- Vendor staff are per vendor (`vendor_users`, Owner / Staff), never an account role. Single vendor edition: two
  roles (Admin, Staff), no subdomains (D26). Commission 3% by default (D23). Counter sales, no commission (D27).
- **Every shop looks like itself** (D19-D21): a shop type (enum) sets layout, attributes, units and defaults; vendors
  choose and arrange brand, template and homepage sections, never code; product video and TikTok links; sharing
  and marketing built in. Plan: [docs/plan-shop-identity-and-marketing.md](docs/plan-shop-identity-and-marketing.md).

## Conventions

GiftNest's, unchanged unless written here: ULID keys, FKs `restrict`, no JSON columns for anything countable,
derived facts read never stored, money as integers in the shop's currency (UGX: whole shillings), UTC stored and
"a day" in the business timezone, phones as digits with the country code (no "+"), enums in `app/Enums` mirrored in
Next, every list paged (5 per page) with state in the URL, every write confirmed and audited, UI copy per GiftNest's
`ui-copy-standard.md`, no em dashes. Storefront pages use our own Tailwind components and the shop's brand tokens
(no PrimeReact there); dashboards use PrimeReact + the platform tokens. Check buyer screens at 393 px.

## Verify

- BE: `cd kheengz-laravel && vendor/bin/pint --dirty --format agent && vendor/bin/phpstan analyse && php artisan test --compact`
- FE: `cd kheengz-nextjs && npx tsc --noEmit -p tsconfig.json && npx eslint app components lib services hooks`

## Working here

Commit and push only when asked, in the repo the change belongs to. Every settled point goes into
`docs/decisions.md` the same turn; docs are updated with the change they describe. `/handoff` writes
`.claude/handoffs/<date>-<title>.md`; `/handoffpass` resumes.
