# Environment Variables

Use `.env.example` as the source template and copy it to `.env.local` for local
development.

```bash
cp .env.example .env.local
```

Never commit `.env.local`, service role keys, private keys, API tokens, webhook
secrets, or deployment tokens.

## Public Site Metadata

| Variable | Scope | Notes |
| --- | --- | --- |
| `NEXT_PUBLIC_SITE_URL` | Browser and server | Local default is `http://localhost:3000`. |
| `NEXT_PUBLIC_ONCHAINKIT_API_KEY` | Browser and server | Optional Coinbase Developer Platform powered OnchainKit key. |

## Supabase

| Variable | Scope | Notes |
| --- | --- | --- |
| `NEXT_PUBLIC_SUPABASE_URL` | Browser and server | Supabase project URL. |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Browser and server | Supabase anon key. Safe for browser use, but still project-specific. |
| `SUPABASE_SERVICE_ROLE_KEY` | Server only | Required by API routes. Never expose in browser bundles. |

## Wallet Auth And Cron

| Variable | Scope | Notes |
| --- | --- | --- |
| `VAELTUS_SESSION_SECRET` | Server only | HMAC secret for wallet session cookies. Use a long random value in production. |
| `CRON_SECRET` | Server only | Shared secret for protected cron endpoints. |
| `HEARTBEAT_STALE_AFTER_MS` | Server only | Heartbeat staleness threshold. Example: `120000`. |
| `HEARTBEAT_CIRCUIT_BREAK_AFTER_MS` | Server only | Auto circuit-break threshold. Example: `600000`. |

## Contracts And Chain Config

| Variable | Scope | Notes |
| --- | --- | --- |
| `NEXT_PUBLIC_REGISTRY_ADDRESS` | Browser and server | `VaeltusRegistry` address. |
| `NEXT_PUBLIC_BUDGET_GOVERNOR_ADDRESS` | Browser and server | `BudgetGovernor` address. |
| `NEXT_PUBLIC_EVENT_ANCHOR_ADDRESS` | Browser and server | `EventAnchor` address. |
| `NEXT_PUBLIC_CHAIN_ID` | Browser and server | Base Sepolia is `84532`. |
| `REQUIRE_ONCHAIN_SYNC` | Server only | Set `true` to fail API writes when required onchain sync fails. |
| `NEXT_PUBLIC_TOKEN_SYMBOL` | Browser and server | Defaults to `VLTS`. |
| `NEXT_PUBLIC_TOKEN_ADDRESS` | Browser and server | Token contract address when available. |

## RPC And Relayer

| Variable | Scope | Notes |
| --- | --- | --- |
| `BASE_RPC_URL` | Server only | Base mainnet RPC endpoint. |
| `BASE_SEPOLIA_RPC_URL` | Server only | Base Sepolia RPC endpoint. |
| `VAELTUS_RELAYER_PRIVATE_KEY` | Server only | Dedicated relayer wallet. Keep funded minimally and scoped by role. |

## Deployment And Verification

| Variable | Scope | Notes |
| --- | --- | --- |
| `BASESCAN_API_KEY` | Local or CI secret | Used by Foundry verification. |
| `PRIVATE_KEY` | Local or CI secret | Generic Foundry private key variable. |
| `DEPLOYER_PRIVATE_KEY` | Local or CI secret | Dedicated deployer wallet. |
| `WATCHER_RELAYER` | Local or CI config | Address granted watcher-style permissions where needed. |
| `ANCHOR_RELAYER` | Local or CI config | Address granted event anchor permissions where needed. |

Use dedicated deployer and relayer wallets. Do not reuse a personal wallet
private key in this repository.

## Alert Delivery

| Variable | Scope | Notes |
| --- | --- | --- |
| `NEYNAR_API_KEY` | Server only | Required for Farcaster notification delivery. |

Webhook and Telegram credentials are stored per agent through notification
settings, not as global env variables.

## SDK And Agent Examples

| Variable | Scope | Notes |
| --- | --- | --- |
| `VAELTUS_AGENT_ID` | Local example | Agent UUID from the dashboard or registration API. |
| `VAELTUS_API_KEY` | Local example | Agent API key. Store securely. |
| `VAELTUS_BASE_URL` | Local example | Local default is `http://localhost:3000`. |
| `AGENT_ID` | Legacy example | Older docs snippets may use this alias. |
| `VLTS_API_KEY` | Legacy example | Older docs snippets may use this alias. |

## Local Testing

| Variable | Scope | Notes |
| --- | --- | --- |
| `TEST_SUPABASE_URL` | Local test | API integration tests skip when unset. |
| `TEST_SUPABASE_SERVICE_ROLE_KEY` | Local test | Use a disposable local or test Supabase project. |
| `BASE_URL` | Local test | Playwright base URL. |
| `PLAYWRIGHT_CHROMIUM_EXECUTABLE_PATH` | Local test | Optional browser binary override. |
