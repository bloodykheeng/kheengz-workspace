# MarzPay

What we know about MarzPay's Merchant API, read on 15 Nov 2026 from its own docs. Confirm the items marked
**Check** on the first sandbox call, then update this file.

- Docs: https://wallet.wearemarz.com/documentation
- OpenAPI spec ("MarzPay Merchant API" 1.0.0): https://wallet.wearemarz.com/documentation/downloads/openapi.yaml
- Pricing (Uganda): https://wallet.wearemarz.com/pricing/ug
- PHP SDK: [`marzpay/php-sdk`](https://packagist.org/packages/marzpay/php-sdk) 1.0.0 (Oct 2025, MIT, 1 star,
  one contributor, no bank transfers or webhook handling). Reference only; we write our own client (D6).

## 1. Base URL and auth

- One base URL for sandbox and live: `https://wallet.wearemarz.com/api/v1`. The **business account's mode**
  decides sandbox or live (new accounts start in sandbox; sandbox responses say `"status": "sandbox"`).
- HTTP Basic: `Authorization: Basic base64(api_key:api_secret)`.
  **Check:** the bank-transfer page shows an `X-API-Key` header instead.
- **IP whitelisting** is required for balance, send money, bank transfer and account endpoints, and payouts need
  the API disbursement permission on the account. The server's IP must be added in the dashboard.

## 2. Collecting a payment

`POST /collect-money`

| Field | Rule |
| --- | --- |
| `amount` | required, 500 to 10,000,000 UGX |
| `reference` | required, **UUID**, unique per collection, max 50 (so our `HN-XXXXXXXX` order number goes in `metadata`, and the payment row keeps the UUID) |
| `country` | required, `UG` (also KE, RW, CD, ZM, CM, BJ, CI, GA, CG, SN, SL) |
| `phone_number` | E.164 with "+", e.g. `+256712345678`; required for mobile money |
| `method` | `mobile_money` (default) or `card` |
| `currency`, `description` (255), `callback_url` (255), `metadata` | optional; metadata = up to 10 one-key objects |

- Response 201: `data.transaction {uuid, reference, status: "processing", provider_reference}`,
  `data.collection {amount {raw, formatted, currency}, provider: "mtn" | "airtel", mode}`.
- Card: `method: "card"`, no phone; the response carries `data.redirect_url` to MarzPay's card page. Needs the
  card service switched on for the account.
- Provider is found from the number: MTN `076x-078x, 077, 078, 031, 039`; Airtel `070x-075x`.
- Lookups: `GET /collect-money/{uuid}`, `GET /collect-money/services`.

Our phones are stored as digits with the country code and no "+" (`256712345678`); the gateway adds the "+".

## 3. Paying out

- Mobile Money: `POST /send-money` (`amount`, `phone_number`, `reference` UUID, `country`; 500 to 10,000,000).
  **Check:** the created object is called `withdrawal` in one doc and `disbursement` in another.
- Bank: `POST /bank-transfer/validate` (`bank_name`, `account_number` → `account_name`, `valid`), then
  `POST /bank-transfer` (`amount` ≥ 2,500, `description`, `bank_name`, `bank_account_number`). Banks:
  `GET /bank-transfer/banks`. Only **one** bank transfer may be pending at a time.
- In our model (D3) payouts are rare: refunds to buyers, and later anything the marketplace pays out itself.

## 4. Results (webhooks)

- Sent to the request's `callback_url` (and/or webhooks set in the dashboard), **only for final results**:
  `collection.completed | failed | cancelled`, `disbursement.completed | failed | cancelled`. Must answer 200.
- Body: `event_type`, `transaction {uuid, reference, status, amount {raw...}, charge, net_amount, provider,
  phone_number}`, `collection {provider_transaction_id, ...}`, `metadata`. Match on `transaction.reference`.
  **Check:** one doc says dashboard webhooks wrap this in `data`.
- **Signing is off by default: switch it on** (Business Settings > Webhooks & Security). Headers
  `X-MarzPay-Timestamp` and `X-MarzPay-Signature: t=<ts>,v1=<hex>`; HMAC-SHA256 of `"<ts>.<raw body>"` with
  the signing secret, compared with `hash_equals`. Unsigned or stale callbacks are refused.
- Retries are not documented: handle duplicates idempotently (GiftNest's locked `PaymentService::apply()`).
- No callback within the expiry window: ask `GET /collect-money/{uuid}` before failing it (GiftNest's
  reconcile step; here a real status call exists).
- `GET /transactions` (filters: type, status, reference, dates; `per_page` ≤ 100) and `GET /balance` for
  reconciling.

## 5. Fees (published, Uganda)

- No setup or monthly fee. Mobile Money collection **3%**, card **5%**, nothing on failed ones.
  **Check:** sample callbacks show 3.5%.
- Mobile Money payout flat fee: 1,000 (to 50,000), 1,500 (to 300,000), 2,800 (to 750,000), 5,000 (to 5,000,000).
- Bank transfer: 5,000 to 16,500 by band.

## 6. What MarzPay does not have (why D3 is shaped the way it is)

No split payments, no vendor sub-wallets, no escrow. ("Sub-accounts" in their pages are staff or branch logins.)
So, per D12, **Kheengz's one account collects** every online payment and **pays each vendor out** with
`send-money` (their Mobile Money number) or `bank-transfer` (their bank account, checked with
`bank-transfer/validate` when they save it). Who owes what lives in our ledger. Things that follow:

- Payouts need the API disbursement permission and the **server's IP whitelisted** in MarzPay.
- Each payout costs a flat fee (§5), so payouts are batched (D12's proposal: one a day per vendor).
- Only one bank transfer can be pending at a time: bank payouts go out one after another from a queue job.
- OlyCash can pay out to Mobile Money too (`docs/olycash.md`), so each wallet funds payouts from its own
  takings; bank payouts go through MarzPay (OlyCash needs bank payees added by hand).
