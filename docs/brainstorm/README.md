# Brainstorm: a reusable commerce platform (first client: Fahim's Home Nest)

Started 15 Nov 2026. **Superseded where [../decisions.md](../decisions.md) differs** (it does on the name, the
money flow and the payment provider: vendors collect their own money through MarzPay, not the shop through
OlyCash). Kept as the research record.

**Visual design board:** https://claude.ai/artifact/42W2wUoNb5fPoWPtgH2ivx (private until shared)

## The idea in five lines

1. Not a one-off site for Fahim: **one platform**, sold to many shop owners, Home Nest being the first.
2. **One codebase, two editions:** a single vendor shop is a marketplace with one vendor. Flip a setting, invite
   sellers. (CS-Cart sells these as two products; Bagisto charges for multi-vendor as a zip extension.)
3. **Easy buying, the East African way:** Buy now or cart, one-page checkout, **no login**, Mobile Money prompt
   or **pay on delivery** (83% of Jumia Uganda shoppers chose cash on delivery), pickup, track by phone.
4. Laravel 13 API + Next.js 16 (storefront + admin + vendor dashboards), Expo app later; GiftNest's
   foundation (payments, OTP, ledger, withdrawals, audit trail, standard tables).
5. MVP = Home Nest live with the marketplace switch working; mobile app after.

## Files

| File | What it covers |
| --- | --- |
| [competitor-teardown.md](competitor-teardown.md) | Bagisto, CS-Cart Multi-Vendor, Shopify, Jumia, Lunar and others: what we take, what we leave, our pitch |
| [product-and-checkout.md](product-and-checkout.md) | Editions, how a buyer buys, the objects in plain words, roles, phases |
| [design-direction.md](design-direction.md) | Platform vs shop brand, three palettes, type, storefront and dashboard rules |
| [stack-and-packages.md](stack-and-packages.md) | Versions checked today, packages and why, payments providers, workspace layout |
| [names-and-domains.md](names-and-domains.md) | Name candidates with domain checks, my recommendation, Home Nest domains |

## To settle before anything is installed

1. **Name.** My view: **Kheeng** (`kheeng.com` is free) with **Kheeng Shop** / **Kheeng Market**. Or **Kheengz** if
   `kheengz.com` (registered 30 Sep 2026) is yours.
2. **Palette.** My view: **Royal** (emerald + gold); the other two become preset storefront themes.
3. **Money flow in the marketplace:** the shop collects and pays vendors out (my view, as GiftNest), or vendors
   collect and owe commission?
4. **Stock per location from day one** (my view) or one number per variant?
5. **Fahim:** what he sells, and how he delivers (own riders, courier, pickup).

Once 1 is settled: create `<name>-workspace` with the Laravel and Next.js apps from the official installers,
copy GiftNest's foundation, write `CLAUDE.md`, `WORK-PLAN.md` and `docs/decisions.md`, and start Phase 0.
