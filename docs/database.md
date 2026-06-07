# Database And RLS

Supabase stores the offchain operational state for VAELTUS. Migrations live in
`supabase/migrations`.

## Main Tables

| Table | Purpose |
| --- | --- |
| `agents` | Registered agents, owner wallet, onchain ID, API key hash metadata, status, circuit-break state, heartbeat timestamp, and metadata. |
| `guardrails` | Per-agent spend limits, transaction limits, tool allowlist, address blocklist, auto circuit-break settings, and threshold. |
| `events` | Agent event history, event type, severity, JSON payload, event hash, and anchored transaction. |
| `alerts` | Operator-facing incidents, severity, message, acknowledgement state, and delivery timestamps. |
| `heartbeats` | Periodic liveness pings from SDK clients. |
| `operator_audit_logs` | Owner actions such as registration, guardrail updates, API key rotation, and circuit changes. |
| `rate_limit_buckets` | Server-side rate-limit state for API routes. |
| `notification_settings` | Per-agent webhook, Telegram, Farcaster, and notification preference settings. |

## Migration Groups

| Migration | Purpose |
| --- | --- |
| `202605230001_phase1_data_foundation.sql` | Base schema for agents, guardrails, events, alerts, heartbeats, RLS, helper policies, and seed-compatible tables. |
| `202605230002_harden_phase1_security_policies.sql` | Follow-up RLS hardening for guardrail writes. |
| `202605230003_testnet_readiness.sql` | Testnet-ready onchain sync fields, operator audit logs, and rate-limit buckets. |
| `202605230004_fix_rate_limit_rpc.sql` | Rate-limit RPC adjustment. |
| `202605230005_notification_settings.sql` | Per-agent notification settings and alert delivery timestamps. |
| `202605230006_farcaster_notifications.sql` | Farcaster notification fields. |
| `202605230007_fix_notification_settings_rls.sql` | Notification setting RLS correction. |
| `20260531041140_harden_security_followups.sql` | Additional security hardening, forced RLS, and API key plaintext safeguards. |

## Local Flow

```bash
supabase start
supabase db reset
```

`supabase db reset` applies migrations and then runs `supabase/seed.sql`.

## API Integration Tests

Use a disposable local or test Supabase project:

```bash
TEST_SUPABASE_URL=http://127.0.0.1:54321
TEST_SUPABASE_SERVICE_ROLE_KEY=
npm run test:api
```

Tests skip automatically when the test Supabase variables are not set.

## RLS Model

RLS is enabled on the operational tables and force-enabled by the hardening
migration. Owner-facing reads and writes are scoped through wallet ownership,
while server routes use the Supabase service role for controlled writes.

Practical rules:

| Access Path | Expected Authorization |
| --- | --- |
| Dashboard reads | Wallet session address owns the agent. |
| Dashboard writes | Wallet session address owns the agent. |
| SDK event and heartbeat writes | API route validates agent API key, then service role inserts rows. |
| Alert delivery | Server-side service role fetches pending alerts and writes delivery timestamps. |
| Audit logs | Server-side service role inserts; owners can read logs for their agents. |

## API Key Storage

Agent API keys should not be stored in plaintext. The API rotates keys by
storing a hash and last-four metadata, then returns the raw key once to the
operator. If a key is exposed, rotate it immediately from the dashboard or
`POST /api/agents/:id/api-key`.
