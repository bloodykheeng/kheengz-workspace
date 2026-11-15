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
  per vendor plan). Worked out on the **items' total, not the delivery fee** (delivery is the boda's). Paid online: taken
  from the payout at once (D22). Pay on delivery: owed when the order is **Delivered** (D14). A cancelled or
  returned order gets its commission back.
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
- **When:** right away, after each payment (settled in D22).

### D13. Fees: a commission on each sale, plus service fees (refines D4)
- **Decision:** Kheengz deducts a **percentage of each sale** plus optional **service fees**. Per-vendor rates
  override the default (D4). The figures are set in D23.
- **Why:** "we shall deduct a percentage on transactions ie 0.g% also things like service fees etc".
- **Gateway fee:** taken from the vendor's payout (settled in D22).

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
  dozen or a bale as a unit). A **counter sale** screen in the vendor dashboard records walk-in sales (settled in
  D27).
- **Why:** "Fahim does wholesale but also walk-ins ... people who come at the shop and buy one by one".

### D18. Web push is optional
- **Decision:** no VAPID keys for now; push notifications in the browser are not needed for the MVP. SMS and
  in-app notifications carry the messages.
- **Why:** "web push keys are optional, they're not needed".

### D19. Shops are customisable and shaped by their type (top priority)
- **Decision:** every shop has its own look and its own address, and a **shop type** (Household & plastics,
  Hardware, Fresh produce, Fashion, General...) that changes how products are described, sold and laid out, not
  only the colours. Vendors choose and arrange (brand, template, homepage sections); they never write code.
- **Why:** "the shops should be customizable so that they all don't look the same ... a hardware shop shouldn't be
  like a vegetable shop ... give all those priorities for our app to be very competitive".
- **Detail and proposals (types, levels, guardrails, phasing):** `docs/plan-shop-identity-and-marketing.md`.

### D20. Products can have video
- **Decision:** vendors upload product videos (processed by us into light MP4s with a poster, played on tap) and
  can show their existing TikToks on a product.
- **Why:** "allow video uploads of products".
- **Detail:** plan §4 (limits proposed: 90 seconds, 150 MB, 4 per product; to confirm).

### D21. Marketing and sharing are built in
- **Decision:** sharing to WhatsApp, TikTok, Facebook, Instagram and others from every product and shop, with link
  previews, status/story cards and links that report where orders came from; promo videos, catalogue feeds and
  resellers follow (plan §5, §7). Posting straight to TikTok waits for TikTok's audit of our app.
- **Why:** "also marketing sharing to TikTok etc, we need to be able to think outside the box".

### D22. Payouts go out right away; every fee comes from the vendor's share
- **Decision:** as soon as a buyer's online payment succeeds, Kheengz pays the vendor's share out automatically,
  no holding and no daily batch. The vendor's payout for a vendor order is:

  `order total (items + delivery) − gateway fee − Kheengz commission (on items) − service fees − payout fee
  − anything the vendor owes (D14)`

  Example: basins UGX 45,000 + delivery 5,000 paid by MarzPay Mobile Money; gateway 3% = 1,500; commission at
  0.5% of 45,000 = 225; payout fee 1,000 → **UGX 47,275** to the vendor's number. Each payout lists these lines for
  the vendor.
- **Why:** "take it from vendor, correct" and "payouts automatic ... fast, right away, why would you need to hold
  someone's money ... it's his shop, they need to feel a similar experience as having a physical shop".
- **What follows:**
  - Paid from the wallet that collected the money (MarzPay or OlyCash, Mobile Money); bank accounts through
    MarzPay. MarzPay allows only one bank transfer pending at a time, so bank payouts queue one after another and
    can take a little longer than Mobile Money.
  - A payout that fails (wrong number, provider down) is retried; the vendor is told and the money shows as due to
    them until it lands. No payout is ever sent twice (locked and idempotent, like payments).
  - A refund after the money went out becomes what the vendor owes, recovered from their next payouts, and past
    the limit their products hide until it is paid (D14).
  - Minimums: MarzPay pays out from UGX 500 to Mobile Money and UGX 2,500 to banks.

### D23. Commission 3% by default; no listing or monthly fees at launch (delegated, from the market)
- **Decision:**
  - **Commission: 3% of the items' total** by default (not on delivery), with a per-vendor rate (D4). bloodykheeng
    can give founding vendors their own rate, Fahim's Home Nest included (0.5% if he wants it for him).
  - **Service fee: a fixed amount per vendor order, UGX 0 at launch**, which the Admin can switch on later.
  - **No listing fees and no monthly fees** at launch; vendor plans with a monthly fee come in Phase 2 (CS-Cart).
- **Why:** "something like 0.5 but you can search online and see what's suitable and we go with that". What
  the market charges (15 Nov 2026): Jumia Uganda 5% to 20% by category (fashion 20% plus UGX 2,000 an order,
  paid out later); Kilimall 5% to 15%, paid monthly; Etsy 6.5%; Amazon 8% to 15%. 0.5% of a UGX 50,000 order is
  UGX 250, less than the SMS, storage and server time that order costs us. 3% keeps Kheengz alive and still far
  below Jumia and Kilimall. With MarzPay's 3% and a payout fee, an online sale costs the vendor about 6% to 8% in
  all, paid out **at once** (D22) instead of monthly; a pay-on-delivery sale costs just the 3%.
- **Sources:** [Jumia Uganda vendor hub](https://vendorhub.jumia.ug/?p=9543),
  [Jumia 2026 cost table](https://vendor.jumia.com/?p=1128),
  [DHL Kenya on marketplace commissions](https://www.dhl.com/discover/en-ke/e-commerce-advice/e-commerce-best-practice/when-is-it-time-for-your-own-online-store-),
  [Marketplace fees compared 2026](https://sellerprofit.pages.dev/blog/marketplace-fees-compared-2026) (third-party).

### D24. The first shop types (delegated, from what sells in Uganda)
- **Decision:** Phase 1 ships six: **Household & home** (plastics, kitchen, appliances: Home Nest),
  **Electronics & phones**, **Groceries & fresh produce**, **Fashion & beauty**, **Hardware & building**, and
  **General** for anything else.
- **Why:** "you can search online" for the first shop types. Electronics is the largest online category in
  Uganda (about 23% of e-commerce revenue, ECDB), so it moves up from Phase 2; Jumia Uganda's biggest campaigns lead
  with smartphones, home appliances, groceries and fashion; hardware was named by bloodykheeng.
- **Sources:** [ECDB: e-commerce in Uganda](https://ecdb.com/resources/sample-data/market/ug/all),
  [Jumia Black Friday 2025 (AllAfrica)](https://allafrica.com/stories/202510310481.html).

### D25. Product videos: limited by size, not length
- **Decision:** no limit on seconds. Each video up to **300 MB**, up to 4 per product. Uploads go **straight to
  storage in parts that resume** after a dropped connection (not through PHP's upload limit), then we make the
  light versions (D20). The size is one config value, so it can be raised.
- **Why:** "let's not limit seconds but MBs". 300 MB is a few minutes of phone video at 1080p.

### D26. Single vendor edition: its own domain, two roles
- **Decision:**
  - **Marketplace:** each vendor's shop at `<shop>.kheengz.com` (own domain later, D19).
  - **Single vendor edition:** no subdomains. The shop is the whole site on the owner's domain, whether we host it
    or they host the software themselves.
  - **Roles, single vendor:** two. **Admin** (the owner: everything, settings and money included) and **Staff**
    (products, stock, orders, counter sales; no settings, payouts or staff). Under the hood the Admin is also the
    one vendor's Owner (D2).
  - **Roles, marketplace:** Admin (Kheengz), Vendor Owner and Vendor Staff (per vendor, `vendor_users`). Buyers
    need no account. Riders come later.
- **Why:** "if it's single vendor then no subdomain needed, cause sometimes one may need to host it themselves or
  buy the software ... for single vendor you put there like 2 roles".

### D27. Counter sales for walk-in customers
- **Decision:** a vendor records an in-shop sale from a counter screen: pick products (scan or search), quantity,
  paid by cash, Mobile Money to their own number, or a Mobile Money prompt through our gateway. Stock comes off
  that shop's location, a receipt prints or goes by SMS. **No commission** (Kheengz did not bring that buyer); a
  prompt through our gateway still carries the gateway fee (D22).
- **Why:** "yah let Fahim be able to record in-shop sales too".

### D28. When an unpaid balance hides a vendor's products
- **Decision:** a vendor's products stop showing when what they owe (commission on pay-on-delivery sales, refunds
  after payout) passes **UGX 50,000**, or when **any amount has been unpaid for 14 days**. Both limits can be set
  per vendor. Products show again as soon as the balance is paid; the vendor is warned by SMS and in the
  dashboard before it happens.
- **Why:** "I go with your suggestion" (UGX 50,000, or any amount left unpaid for 14 days, adjustable per vendor).

### D29. Record where visitors come from
- **Decision:** every audit entry records the IP, country, region, city, coordinates, device, platform and
  browser (the foundation, from GiftNest). Each **order** and vendor sign-up will also store the buyer's IP,
  country, region, city, device, platform, browser and **how they arrived** (share-link source, referrer) as
  plain columns, so reports count sales by place and by source. Places come from **DB-IP City Lite**, a database
  file read on our own server: free with no account or key, no rate limit, no IP sent to a third party,
  commercial use allowed (CC BY 4.0) with a link to DB-IP on pages that show places. Refreshed monthly by the
  scheduler. Not ip-api.com (the package's default and Alpha's: free tier non-commercial, 45 lookups a minute,
  plain HTTP). Not MaxMind GeoLite (first choice, dropped the same day after bloodykheeng checked MaxMind's
  site: free terms cover a company's internal use only, and showing places to vendors needs the USD 10,000 a
  year commercial licence).
- **Why:** "there are things we need to record on our clients, i.e. country, regions, IP etc".
- **To do once per server:** `php artisan location:update` (about a minute, 120 MB), then the scheduler keeps it fresh.
- **Detail:** `kheengz-laravel/docs/visitor-origin.md`.

### D30. The web app: static pages, the session read in the browser
- **Decision:** keep Next 16.4's Cache Components (on from `create-next-app`): every page, storefront and
  dashboard, is prerendered into a static shell that opens at once. The server never reads the session
  cookies; `proxy.ts` guards `/dashboard` from them before render, and the dashboard reads the session and
  the theme in the browser behind a loading cover. Only `/dashboard` needs a session: every other path is the
  storefront, open to all (an unknown path shows Page not found, never a seller's sign-in). PrimeReact's
  theme loads only on dashboard and sign-in pages, inside a CSS cascade layer, so storefront pages never
  download it.
- **Why:** buyers are on phones and mobile data, so storefront pages must load from the shell; reading the
  session on the server (GiftNest's way) would make every page wait on the request, and Next would refuse
  instant navigation into the dashboard. Nothing on a dashboard page needs the server to know the user: the
  data is fetched in the browser and the API checks every request.
- **Detail:** `kheengz-nextjs/docs/sessions-and-cache-components.md`.
