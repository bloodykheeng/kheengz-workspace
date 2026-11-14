# Decisions

What has been settled, newest last. Each entry: the decision, why (bloodykheeng's words in quotes), and where the
detail lives. Once settled, a point is closed unless bloodykheeng reopens it.

---

## 2026-11-15

### D1. The platform is called Kheengz
- **Decision:** the platform's name is **Kheengz** (plural). Repos and folders use `kheengz-*`.
- **Why:** "i prefer plural kheengz" ... "kheengz mart but kheengz is better".
- **Open:** `kheengz.com` was registered on 30 Sep 2026; confirm it is bloodykheeng's. Free on 15 Nov 2026:
  `kheengz.shop`, `.store`, `.africa`, `.app`, `.co`, `.io` (`docs/brainstorm/names-and-domains.md`).

### D2. One codebase; a single vendor shop is a marketplace with one vendor
- **Decision:** every installation has vendors. Single vendor = one vendor, vendor screens hidden. Marketplace =
  vendor sign-up switched on. No second product, no migration between them.
- **Why:** "a reusable software that i can sell ... as a single vendor shop or a multi vendor".
- **Detail:** `docs/brainstorm/product-and-checkout.md` §1.

### D3. ~~Each vendor collects their own money~~ (replaced the same day by D12)
- **Decision:** buyers pay the vendor directly (the vendor's own MarzPay account for Mobile Money and card, the
  vendor's boda rider for pay on delivery). The marketplace never holds vendors' money. Each delivered vendor order
  **adds the commission the vendor owes** to that vendor's commission account (a ledger); the vendor pays it to the
  marketplace's MarzPay account through a Mobile Money prompt from their dashboard.
- **Why:** "each vendor collects and we get commission or a percent". It also fits pay on delivery: the cash is in
  the vendor's hand anyway. MarzPay has no split payments or vendor sub-wallets (`docs/marzpay.md` §6), so a
  marketplace-collects model would mean holding everyone's money and paying a disbursement fee on every payout.
- **Consequence:** a vendor without a connected MarzPay account can still sell with pay on delivery.
- **Still to settle:** how often commission is due (weekly or monthly statement) and what happens when it is
  overdue (my proposal: a credit limit per vendor; past it, their products stop showing until they pay).

### D4. Commission: a percentage plus an optional fixed fee (delegated, from Bagisto and CS-Cart; refined by D13)
- **Decision:** the marketplace sets a default **percentage** and an optional **fixed fee per vendor order**; any
  vendor can have their own rate instead (Bagisto: global rate + per-seller override; CS-Cart: percent + fixed fee
  per vendor plan). Worked out on the **items' total, not the delivery fee** (delivery is the boda's). It becomes
  owed when the vendor order is **Delivered**; a cancelled order owes nothing, a return reverses it.
  Vendor plans with a monthly fee (CS-Cart) come in Phase 2.
- **Why:** "commission or a percent ... get idea from best market places like bagisto or cscart".

### D5. Stock is kept per location (delegated, from Bagisto and CS-Cart)
- **Decision:** quantity is held per variant **per location** (a vendor's shop, store room or warehouse). Each
  vendor gets one location when created; more can be added. "In stock" on the storefront is the sum.
- **Why:** "per location ... I don't know what's best but get idea from best market places". Bagisto has
  inventory sources in its free core; CS-Cart sells multi-warehouse stock as a feature. Adding it later would mean
  moving every stock figure.

### D6. MarzPay is the payment provider
- **Decision:** payments go through **MarzPay** (Mobile Money MTN/Airtel, card; payouts to Mobile Money and banks).
  bloodykheeng's business account was switched to live on 15 Nov 2026. Provider calls only in `app/Payments`
  behind a `PaymentGateway` interface (GiftNest's design), so another provider is one class.
  We write our own thin client against MarzPay's OpenAPI spec; their PHP SDK (one release, one contributor)
  is a reference only.
- **Why:** "i registered to marz pay and they even have bank etc ... we shall be using it".
- **Detail:** `docs/marzpay.md`.

### D7. Delivery for the MVP: the vendor's own boda riders
- **Decision:** each vendor sets their delivery areas and fees and their pickup address. When dispatching, the
  vendor records the rider (name, phone; saved riders are reused). The buyer gets an SMS with the rider's number.
  Pay on delivery cash goes to the rider for the vendor. No rider accounts or rider app in the MVP.
- **Why:** Fahim "sells plastics, he usually gives to boda and they deliver".

### D8. Palette: Royal (emerald and gold), provisional
- **Decision:** the platform uses **Royal** unless bloodykheeng picks another; Cobalt Market and Busuuti become
  preset storefront themes. Board: https://claude.ai/artifact/42W2wUoNb5fPoWPtgH2ivx
- **Why:** proposed 15 Nov 2026, not objected to. Reopen freely.

### D9. Workspace
- **Decision:** `fahim/kheengz-workspace` (its own repo) holding `kheengz-laravel` and `kheengz-nextjs` (each its
  own repo, git-ignored by the workspace); `kheengz-mobile` (Expo) in Phase 3. Installed with the official
  installers on 15 Nov 2026. The foundation comes from GiftNest.
- **Git:** GitHub `bloodykheeng/kheengz-workspace`, `kheengz-laravel`, `kheengz-nextjs` (created by bloodykheeng),
  branch `main` (bloodykheeng's own product, as Elevate Pesa's workspace; `farouk` is for client repos).

### D10. Firebase: one project per environment
- **Decision:** development uses Firebase project `kheengz-dev`, production `kheengz-6f97b`, both under
  bloodykheeng's Google account. The service account goes in the API's `FIREBASE_*` lines read through
  `config/services.php` (Alpha's pattern); the web config in `NEXT_PUBLIC_FIREBASE_*`. Secrets never in git:
  dev in the git-ignored env files, production in `fahim/deployment/`. Both keys validated on 15 Nov 2026.
- **Why:** "i've created ... firebase ... set them in well for dev and prod".
- **Detail:** `kheengz-laravel/docs/environment-setup.md`.

### D11. PrimeReact stays on 10 for now (a held-back major)
- **Decision:** `primereact@10` and `primeicons@7`, not the newest 11 / 8.
- **Why:** PrimeReact 11 (May 2026) ships no CSS and drops about a third of v10's modules (117 to 80);
  moving needs hand work on every screen. The GiftNest dashboard foundation we copy, and the coding notes on
  PrimeReact traps, are all v10. Revisit as one planned move across all projects, not inside the MVP.
- Every other package took its latest major (Firebase 13, `@hookform/resolvers` 7, Pest 5, Spatie permission 8).
- **npm audit (15 Nov 2026):** the remaining findings are `minimatch` under ESLint (dev only) and `@grpc/grpc-js`
  under Firebase's Firestore module (not used; the advisories concern gRPC servers). Their fixes need breaking
  `--force` upgrades; revisit when Firebase or ESLint update them.

### D12. Kheengz collects online payments and pays vendors out (replaces D3)
- **Decision:** the payment accounts are **Kheengz's**, not the vendors'. Buyers pay online into Kheengz's
  gateway account; each vendor only gives a **payout destination**: a Mobile Money number or a bank account
  (validated with MarzPay's bank lookup before it is saved). Kheengz pays the vendor's share out through MarzPay
  (`send-money` / `bank-transfer`), keeping its commission and fees. Pay on delivery stays with the vendor's boda,
  and that commission becomes a debt the vendor pays (D14).
- **Why:** "making vendors need to have MarzPay is vague, it's me who will have MarzPay, other people will just
  provide their mobile money numbers or banks where money will go direct ... that's more smooth".
- **Consequences:**
  - **Each gateway pays out from its own takings**: Mobile Money payouts through MarzPay (`send-money`) or OlyCash
    (`/money/pay`), from whichever wallet holds the vendor's money; **bank payouts through MarzPay**, because
    OlyCash needs every bank payee added by hand in its admin first. MarzPay and OlyCash "work hand in hand"
    (bloodykheeng): if one wallet runs short, the other covers. The dashboard shows each wallet's balance against
    what is owed to vendors. Detail: `docs/olycash.md`, `docs/marzpay.md`.
  - Kheengz now holds buyers' money until it is paid out: a ledger per vendor (GiftNest's `transactions`), every
    payout a row, reconciled against MarzPay's `GET /transactions`.
- **Proposed, to confirm:** *when* the money goes out. My view: **automatically once a day**, the vendor's balance
  from orders marked **Delivered** in one payout. Two reasons. Paying before delivery means a refund can't be
  taken back. And every payout costs UGX 1,000 or more (Mobile Money) or 5,000 or more (bank) from MarzPay, which is
  20% of a UGX 5,000 basin sold on its own. A vendor can still ask for their balance at once, paying that fee.

### D13. Fees: a commission on each sale, plus service fees (refines D4)
- **Decision:** Kheengz deducts a **percentage of each sale** (set by the Admin; "0.g%" in the message, exact
  figure to set) plus optional **service fees**. Per-vendor rates override the default (D4).
- **Why:** "we shall deduct a percentage on transactions ie 0.g% also things like service fees etc".
- **To confirm, because the money must add up:** MarzPay keeps **3%** of every Mobile Money collection (5% card),
  more than a commission under 1%. Unless someone else carries it, Kheengz loses money on every online sale.
  My view: the vendor's payout = sale − gateway fee − Kheengz's commission, shown line by line on each payout
  (how Jumia and Stripe-based marketplaces do it). The alternative is a buyer service fee shown at checkout.
  OlyCash already takes a `fee_paid_by` setting (buyer or merchant).

### D14. Pay on delivery: commission owed, products hidden when unpaid
- **Decision:** a vendor's pay-on-delivery sales (and any sale outside our gateways) add the commission to what
  they **owe**. Past a limit their products stop showing until they pay (Mobile Money prompt from their
  dashboard). Proposed on top: what a vendor owes is first taken from their next online payout, so vendors with
  online sales never fall behind.
- **Why:** "yah their products stop [showing] until they pay, those who do pay on delivery and are not using our
  payment gateways".

### D15. Two gateways, the buyer picks
- **Decision:** checkout offers **MarzPay** and **OlyCash** (whichever the Admin has switched on), the buyer picks.
  Card only through MarzPay. Both behind `PaymentGateway`; outcomes only through the locked, idempotent
  `PaymentService` (GiftNest).
- **Why:** "we are using both payments OlyCash or MarzPay so it will be up to the buyer to go with what they want".

### D16. First launch: the Kheengz marketplace, Fahim's Home Nest as the first vendor
- **Decision:** the first deployment is the **Kheengz marketplace** on `kheengz.com` (bloodykheeng's; set up at
  deployment, last). Fahim's shop **Home Nest** is vendor number one.
- **Why:** "Fahim will be the first vendor, his shop is Home Nest ... domain will be kheengz.com, done last".

### D17. Wholesale and walk-in customers
- **Decision:** a variant can have **quantity price breaks** (e.g. 1 to 11 at one price, 12 and more cheaper; a
  dozen or a bale as a unit). Proposed: a **counter sale** screen in the vendor dashboard so walk-in sales take
  stock off the right location, with no commission (Kheengz did not bring that buyer).
- **Why:** "Fahim does wholesale but also walk-ins ... people who come at the shop and buy one by one".

### D18. Web push is optional
- **Decision:** no VAPID keys for now; push notifications in the browser are not needed for the MVP. SMS and
  in-app notifications carry the messages.
- **Why:** "web push keys are optional, they're not needed".
