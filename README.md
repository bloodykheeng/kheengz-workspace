# Kheengz workspace

**Kheengz** is a commerce platform sold as a single vendor shop or a multi-vendor marketplace: Buy now or cart,
checkout with no account, MarzPay Mobile Money or pay on delivery, orders split per vendor, vendors paying the
marketplace a commission. First client: Fahim (Home Nest, plastics). This repo holds the instructions, the plan,
the decisions and the session handoffs. **No application code lives here.**

| Repository | Folder | Stack | Local |
| --- | --- | --- | --- |
| [kheengz-workspace](https://github.com/bloodykheeng/kheengz-workspace) | this one | instructions, plan, decisions, handoffs | |
| [kheengz-laravel](https://github.com/bloodykheeng/kheengz-laravel) | `kheengz-laravel/` | Laravel 13.42, PHP 8.4, Sanctum, Reverb, Spatie permission 8 / activitylog 5 / query-builder 7, Scramble, kreait Firebase, Pest 5, Larastan 3, Boost 2 | http://localhost:8000 |
| [kheengz-nextjs](https://github.com/bloodykheeng/kheengz-nextjs) | `kheengz-nextjs/` | Next.js 16.4.6, React 19.3, TanStack Query 5, react-hook-form + zod 4, PrimeReact 10, Tailwind 4, laravel-echo, Firebase 13, Playwright | http://localhost:3000 |

Work happens on branch **`farouk`** in all three (`main` holds only the first commits). Read [CLAUDE.md](CLAUDE.md) first, then [WORK-PLAN.md](WORK-PLAN.md) and
[docs/decisions.md](docs/decisions.md).

```bash
git clone -b farouk https://github.com/bloodykheeng/kheengz-workspace.git kheengz-workspace
cd kheengz-workspace
git clone -b farouk https://github.com/bloodykheeng/kheengz-laravel.git kheengz-laravel
git clone -b farouk https://github.com/bloodykheeng/kheengz-nextjs.git kheengz-nextjs
```

Then open `kheengz.code-workspace`.

## First-time setup (Windows + Laragon)

Needs PHP 8.4, Composer, Node 22, MySQL 8 (Laragon's).

```bash
# API
cd kheengz-laravel
composer install
cp .env.example .env          # then fill FIREBASE_* (kheengz-dev) and MarzPay sandbox keys: docs/environment-setup.md
php artisan key:generate
mysql -uroot -e "CREATE DATABASE kheengz CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci"
php artisan migrate --seed
php artisan location:update   # DB-IP City Lite for visitor places (about a minute, 120 MB)

# Web
cd ../kheengz-nextjs
npm install
# .env.development and .env.production are not in git: copy them from ../../deployment/server-env/ or
# rebuild them from .env.example
```

## Running

Each in its own terminal: `php artisan serve`, `php artisan reverb:start --debug`, `php artisan queue:work`,
`php artisan schedule:work` (API), and `npm run dev` (web).

## Environments and secrets

| | Firebase project | Where the secrets live |
| --- | --- | --- |
| Development | `kheengz-dev` | API `.env`, web `.env.development` (both git-ignored) |
| Production | `kheengz-6f97b` | `fahim/deployment/` beside this workspace, outside every repo |

Both Firebase projects belong to bloodykheeng's Google account. Production domain and server: not chosen yet.
