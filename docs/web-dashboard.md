# Web Dashboard

The web dashboard lives in `apps/web`. It is a Next.js application with landing
pages, wallet auth, dashboard routes, API routes, and tests.

## Main Surfaces

| Route | Purpose |
| --- | --- |
| `/` | Landing page. |
| `/core-features` | Product capability overview. |
| `/docs` | In-app developer docs page. |
| `/dashboard` | Operator overview, status grid, alerts, and registration flow. |
| `/dashboard/agents` | Agent list. |
| `/dashboard/agents/:id` | Single-agent detail view. |
| `/dashboard/agents/:id/events` | Agent event history. |
| `/dashboard/agents/:id/alerts` | Agent-specific alerts. |
| `/dashboard/agents/:id/guardrails` | Agent-specific guardrail editor. |
| `/dashboard/alerts` | Incident hub for active and acknowledged alerts. |
| `/dashboard/analytics` | Charts and summary analytics. |
| `/dashboard/guardrails` | Consolidated guardrail management. |
| `/dashboard/settings` | Wallet, API key, audit log, and notification controls. |

## Operator Model

The dashboard is wallet-native:

1. A wallet requests a nonce from `/api/auth/nonce`.
2. The wallet signs the generated message.
3. `/api/auth/verify` validates the signature and sets the wallet session.
4. Dashboard routes authorize writes by matching the session address to
   `agents.owner_address`.

## Key Components

| Path | Purpose |
| --- | --- |
| `components/common` | Shared buttons, cards, badges, modal, wallet connection UI, and brand mark. |
| `components/dashboard` | Agent cards, alerts, charts, budget meter, event timeline, global search, guardrail editor, and notification settings. |
| `components/landing` | Landing page sections. |
| `components/layout` | Dashboard header and sidebar. |
| `lib/auth.ts` | Wallet session, API key, nonce, and ownership helpers. |
| `lib/supabase.ts` | Supabase admin and client setup. |
| `lib/onchain.ts` | Contract sync and relayer helpers. |
| `lib/alert-delivery.ts` | Webhook, Telegram, and Farcaster alert delivery. |
| `lib/rate-limit.ts` | Rate limit bucket helpers. |

## Local Commands

```bash
npm run dev:web
npm run build:web
npm run start:web
npm run lint --workspace=apps/web
npm run test:api --workspace=apps/web
npm run test:e2e --workspace=apps/web
```

For LAN or mobile production checks:

```bash
npm run build:web
npm run start:web -- -p 3000 -H 0.0.0.0
```

## Test Notes

API integration tests skip automatically unless these variables are set:

```bash
TEST_SUPABASE_URL=
TEST_SUPABASE_SERVICE_ROLE_KEY=
```

Playwright uses:

```bash
BASE_URL=http://localhost:3000
PLAYWRIGHT_CHROMIUM_EXECUTABLE_PATH=
```
