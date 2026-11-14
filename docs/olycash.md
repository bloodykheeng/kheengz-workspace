# OlyCash

From OlyCash's own document "OlyCash API Documentation [Quick Sell Section]" (API V2.7, ©2022), kept at
`D:\coding\bloodykheeng projects\olycash\olycash_api__quicksell_06012023.pdf`. Read 15 Nov 2026. GiftNest
already collects through it (GiftNest `giftnest-laravel/docs/payments.md`, `OlyCashGateway`): copy that code.

## Connecting

- `POST https://api.olycash.com/v2/authorize` with `olycash_id`, `password` (optional `intent`: `refresh` |
  `close`) → `key`, `public_key`. The key does not expire; `refresh` replaces it.
- Every other call: headers `Authorization: <key>`, `Account: <olycash id>`, JSON body.
- Replies carry `message`: `"OK"` or an error text; HTTP is 200 (404 only for a wrong endpoint).
  Times are epoch seconds; currencies three-letter codes.

## Endpoints

| # | Call | Sends (`*` required) | Gets |
| --- | --- | --- | --- |
| 1 | `POST /purchases/pay` (collect) | `*account_id`, `*item_name` or `item_id`, `quantity`, `*total`, `price`, `*currency`, `*buyer_olycash_id` or buyer first/last name, email, telephone, `*method` (`mobile_money`, `wallet`, `debit_code`, `card`), `*post_response_url`, `fee_paid_by` (`payer` default / `payee`), `frequency`, `sale_split` | `message`, `purchase_id`, `status` |
| 2 | `GET /transactions/sales` | `*account_id`, `phrase`, `buyer_id`, `limit`, `offset` | `count`, `data[]` {date, status, code, fee, transaction_amount, order_id, buyer, seller} |
| 3 | `POST /bank/payout` (pay out to a bank) | `*account_id`, `*olycash_pin`, `*amount`, `*currency`, `*payee_contact`, `fee_paid_by`, `note` | `message`, `withdrawal_id`, `breakdown` {currency, total, charge, fee, net, transfer} |
| 4 | `POST /money/pay` (pay out to Mobile Money) | `*account_id`, `*payee_contact` (the phone), `*payment` {amount, currency}, `*method` = `mobile_money`, `*pin` | `message` |
| 5 | `POST /purchases/instruction` | `*account_id`, `*purchase_id`, `*role` (`buyer` / `shipper`), `*details` (≤150 chars) | `message` |

## What this means for Kheengz

- **Payouts work through OlyCash too** (corrects an earlier note that said it could not). Mobile Money payouts
  can go to any phone. **Bank payouts need the payee added by hand first** in OlyCash's admin
  (admin.olypages.com > Paying > Payees) and are addressed by that payee's email or phone, so bank payouts go
  through MarzPay, which takes the account number directly.
- Each payout call needs the **account PIN**: it lives in the API `.env` only (`OLYCASH_PIN`), never logged.
- `sale_split` (`<olycash-id>=<percent>|...`) splits a sale between **OlyCash accounts**. Our vendors give a phone
  or bank account, not an OlyCash account (D12), so we don't use it; it would matter only if a vendor already
  has OlyCash.
- `fee_paid_by` lets the payer or the payee carry OlyCash's fee, on collections and bank payouts (D13).
- **Not in the document:** payout fees, payout results by webhook (the Mobile Money payout answers only
  `message`), and a status call for one payout. Check with OlyCash before relying on it for payouts.
- Results of collections arrive form-encoded at `post_response_url`, **unsigned** (GiftNest puts a 48-character
  token in the URL and checks it with `hash_equals`).
