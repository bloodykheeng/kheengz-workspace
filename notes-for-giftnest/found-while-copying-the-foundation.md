# Found in GiftNest while copying its foundation into Kheengz (15 Nov 2026)

From the Kheengz workspace. Nothing in GiftNest was changed. Per the playbook: verify each point in
GiftNest's own code, search current practice, fix what holds, then mark this note **Resolved** at the
top (date, commits, what was decided, what did not apply).

## 1. Reference numbers still use "highest + 1" (important)

- **Where:** `app/Traits/HasReferenceNumber.php` (GNA account numbers, TX, WR).
- **Effect:** the next number is worked out from the highest still in the table. Delete the newest record
  and its number goes to the next one, while the old number already sits in an SMS, an email or the
  audit trail. NICE found it (5 Nov 2026); Alpha replaced it on 12 Nov 2026.
- **Fix used in Kheengz:** Alpha's `App\Support\SerialCounter` + the `serial_counters` table
  (Alpha `docs/reference-numbers.md`, `tests/Feature/SerialNumbersTest.php`).
- **Test that proves it:** delete the newest record, create one more, its number must not come back
  (Kheengz `tests/Feature/SerialCounterTest.php`).

## 2. Two config files carry a search-and-replace accident

- `config/permission.php`: `'teams' => false` became `'groups' => false` and the `'team'` model key became
  `'group'` (with "Groups feature" in the comments). The package reads `teams` and `team`; the renamed keys
  are ignored. Harmless while teams are off, wrong the day they are needed.
- `config/cache.php`: the file store's `lock_path` became `storage_path('framework/cache/data')` (Laravel's
  is `framework/cache/locks`), and a comment reads "contestant types of items". Locks now share the data
  folder, so `cache:clear` also wipes held locks.
- **Fix used in Kheengz:** Laravel's published originals.

## 3. Next.js below the November security release

- `giftnest-nextjs/package.json` pins `next` **16.3.5**. The 3 Nov 2026 Next.js security release fixed 7
  advisories in 16.4.5 (and 15.5.29). Kheengz is on 16.4.6. (Alpha's is 16.4.2: also below.)

## 4. IP lookups go to ip-api.com's free (non-commercial) service

- GiftNest has no `config/location.php`, so `stevebauman/location` uses its default driver, ip-api.com,
  whose free tier is for non-commercial use and is HTTP only. Every audit entry sends the visitor's IP there.
- **Fix used in Kheengz:** DB-IP City Lite read from local disk (`App\Support\DbIpCityLite`, which extends
  the package's MaxMind driver since the file format is the same; no account or key; CC BY 4.0, credit DB-IP;
  refreshed monthly by the scheduler; no fallbacks), `LOCATION_TESTING=false` (when true, local requests are placed at a Google address in the
  US). Kheengz `docs/visitor-origin.md`. MaxMind GeoLite is not the answer: its free terms cover internal use
  only. Alpha has the same ip-api.com default (with MaxMind as an unconfigured fallback).

## 5. Small

- `config/activitylog.php`'s retention comment still describes SDS's "ANNUAL inspections".

## 6. Found while copying the web foundation

- **Lint rules switched off.** `eslint.config.mjs` turns off `no-explicit-any`, `set-state-in-effect` and
  `no-css-tags`, and two files carry `eslint-disable` for `exhaustive-deps`. Kheengz runs the full rule set:
  58 problems fixed at the source (typed errors as `AxiosError<ApiErrorBody>`, `useResetWhen` /
  `useResetOnOpen` for state that follows a value, `useSyncExternalStore` for browser reads, `useEffectEvent`
  in ExportDialog). See `kheengz-nextjs/docs/dashboard-patterns.md` section 10.
- **`xlsx` 0.18.5 from npm.** SheetJS stopped publishing to npm; 0.18.5 has known advisories (prototype
  pollution, ReDoS). Kheengz installs `https://cdn.sheetjs.com/xlsx-0.20.3/xlsx-0.20.3.tgz`.
- **Login types say `user: { data: UserResource }`; the API sends `user` flat** (a JsonResource nested in
  a plain `response()->json` is not wrapped). GiftNest's RowForm reads both shapes to cope; the types should
  say what the API sends.
- **`proxy.ts` sends any unlisted path to /login when signed out**, so a mistyped public link shows a sign-in
  page instead of Page not found.
- **Cookie and storage names:** the theme cookie is plain `theme` (shared by every app on localhost), and
  `lib/rememberedAccount.ts` stores under `sds_remembered_account`.
- **Dead SDS code:** `lib/queryKeys.ts`, `hooks/useRealtimeResource.ts`, `hooks/useChannel.ts` and
  `lib/liveRefresh.ts` are imported by nothing; `lib/echo.ts` still routes SDS's visitor-chat presence
  channel; `UserMenu` keeps an always-empty "Assigned scope" block; `AppSidebar` gates on SDS capabilities.
- **The root layout reads cookies** (theme, session). Fine without Cache Components; with them on (Next 16.4's
  default) it makes every page dynamic. Kheengz's way: `kheengz-nextjs/docs/sessions-and-cache-components.md`.
