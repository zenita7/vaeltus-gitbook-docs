# Security Model

VAELTUS is designed around wallet ownership, one-time API key handling, scoped
server secrets, RLS, rate limiting, and onchain auditability.

## Authentication

| Actor | Auth Mechanism |
| --- | --- |
| Operator | Wallet signature challenge, then HMAC-signed session cookie. |
| Agent runtime | Bearer API key tied to one agent. |
| Cron job | Shared `CRON_SECRET`. |
| Relayer | Server-side wallet private key with scoped contract roles. |

## Authorization

Operator actions check ownership by comparing the wallet session address to the
agent owner address. Agent runtime actions check that the bearer API key belongs
to the requested agent ID.

Owner-only actions include:

| Action | Route |
| --- | --- |
| Register agent | `POST /api/agents/register` |
| Pause, resume, or circuit-break | `PATCH /api/agents/:id` |
| Reset circuit breaker | `POST /api/agents/:id/reset-circuit` |
| Rotate or revoke API key | `POST` or `DELETE /api/agents/:id/api-key` |
| Update guardrails | `PUT /api/guardrails/:agentId` |
| Acknowledge alerts | `PATCH /api/alerts` and `PATCH /api/alerts/:id` |
| Update notification settings | `PUT /api/notification-settings` |

## Secrets

Never commit these values:

```text
SUPABASE_SERVICE_ROLE_KEY
PRIVATE_KEY
DEPLOYER_PRIVATE_KEY
VAELTUS_RELAYER_PRIVATE_KEY
BASESCAN_API_KEY
CRON_SECRET
VAELTUS_SESSION_SECRET
NEYNAR_API_KEY
VAELTUS_API_KEY
GitHub tokens
npm tokens
PyPI tokens
webhook secrets
Telegram bot tokens
```

Use deployment platform secrets, GitHub Actions secrets, Supabase dashboard
settings, npm/PyPI trusted publishing where available, or local `.env.local`
files.

## API Keys

Agent API keys are returned once and then stored as a hash with last-four
metadata. Treat them like production credentials.

Recommended handling:

1. Store the key in the agent runtime secret manager.
2. Rotate the key if it appears in logs, screenshots, build artifacts, or Git.
3. Revoke old keys when decommissioning an agent.
4. Never expose API keys in browser bundles.

## Rate Limits

The API uses rate-limit buckets for sensitive and high-volume paths.

| Path | Limit |
| --- | --- |
| `POST /api/auth/nonce` | 10 requests per minute per wallet. |
| `POST /api/agents/register` | 5 requests per hour per wallet. |
| `POST /api/agents/:id/events` | 120 requests per minute per agent. |
| `POST /api/agents/:id/heartbeat` | 30 requests per minute per agent. |
| `POST /api/agents/:id/api-key` | 5 rotations per hour per wallet and agent. |

## Onchain Roles

Use dedicated relayer wallets and grant only required roles:

| Contract | Role | Purpose |
| --- | --- | --- |
| `VaeltusRegistry` | `REGISTRAR_ROLE` | Register agents for owners after offchain verification. |
| `VaeltusRegistry` | `WATCHER_ROLE` | Pause or circuit-break agents when needed. |
| `VaeltusRegistry` | `ANCHOR_ROLE` | Increment registry anchor counters. |
| `BudgetGovernor` | `BUDGET_MANAGER_ROLE` | Sync budget settings from API to contract. |
| `EventAnchor` | `ANCHOR_ROLE` | Anchor event hashes. |

## Production Checklist

Before deploying:

1. Confirm `.env.local` is ignored and not staged.
2. Confirm production secrets are set only in deployment providers.
3. Confirm contract addresses match the selected chain.
4. Confirm relayer wallets are funded minimally and role-scoped.
5. Run secret scanning before pushing sensitive branches.
6. Rotate any credential that ever reached a remote.

## Incident Response

If a secret is exposed:

1. Revoke or rotate it immediately at the provider.
2. Move funds and roles away from exposed wallet keys.
3. Rotate agent API keys from the dashboard.
4. Invalidate deployment secrets and CI tokens.
5. Review `docs/SECRET_ROTATION.md` for the project-specific rotation flow.
