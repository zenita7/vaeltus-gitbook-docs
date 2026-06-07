# Core Workflows

This page explains the main runtime workflows that connect the dashboard, API,
SDKs, Supabase, and contracts.

## Wallet Authentication

1. The browser calls `POST /api/auth/nonce` with a wallet address.
2. The server validates the address, creates a nonce, and stores a signed nonce
   cookie.
3. The wallet signs the challenge message.
4. The browser calls `POST /api/auth/verify` with address, message, signature,
   and wallet metadata.
5. The server verifies the signature, consumes the nonce, and sets the wallet
   session cookie.
6. Dashboard routes use the wallet session to authorize owner-only actions.

Related routes:

| Method | Route | Purpose |
| --- | --- | --- |
| `POST` | `/api/auth/nonce` | Create wallet sign-in challenge. |
| `POST` | `/api/auth/verify` | Verify signature and create session. |
| `GET` | `/api/auth/session` | Read active wallet session. |
| `POST` | `/api/auth/logout` | Clear wallet session. |

## Agent Registration

1. The operator connects a wallet and signs in.
2. The dashboard calls `POST /api/agents/register`.
3. The API checks the wallet session and validates the owner address.
4. If a matching active agent exists for the wallet, the API rotates and returns
   a fresh API key for that agent.
5. If no agent exists, the API inserts the agent row, creates default guardrails,
   attempts onchain registration, and writes an operator audit log.
6. The API key is returned once. The operator must store it securely.

## Event Logging

1. An agent calls the TypeScript or Python SDK.
2. The SDK checks local guardrails before sending risky events.
3. The SDK sends `POST /api/agents/:id/events` with `Authorization: Bearer`.
4. The API validates the key, applies rate limiting, verifies agent status, and
   stores the event in Supabase.
5. Critical events and guardrail violations create alerts.
6. Critical events and violations attempt onchain hash anchoring.
7. Notification delivery runs for newly created alerts.

## Heartbeats

1. The SDK starts a periodic heartbeat loop after `connect()`.
2. The SDK sends `POST /api/agents/:id/heartbeat`.
3. The API validates the key, rate-limits the call, rejects paused or
   circuit-broken agents, updates `agents.last_heartbeat`, and inserts a
   heartbeat record.
4. The dashboard uses this data to show stale or degraded status.
5. The heartbeat monitor cron can create alerts or circuit-break agents that
   have missed their heartbeat threshold.

## Guardrail Updates

1. The operator edits guardrail settings in the dashboard.
2. The dashboard calls `PUT /api/guardrails/:agentId`.
3. The API validates wallet ownership.
4. The API normalizes tool names and blocked addresses.
5. The API upserts Supabase guardrails.
6. The API attempts to sync budget limits to `BudgetGovernor`.
7. The API writes an operator audit log.

Guardrail fields:

| Field | Meaning |
| --- | --- |
| `maxSpendPerHour` / `max_spend_per_hour` | Hourly spend limit. |
| `maxSpendPerDay` / `max_spend_per_day` | Daily spend limit. |
| `maxTxPerMinute` / `max_tx_per_minute` | Transaction rate limit. |
| `allowedTools` / `allowed_tools` | Tool allowlist. Empty means no allowlist restriction. |
| `blockedAddresses` / `blocked_addresses` | EVM addresses rejected before or during event handling. |
| `autoCircuitBreak` / `auto_circuit_break` | Whether repeated critical alerts can halt the agent. |
| `criticalThreshold` / `critical_threshold` | Critical alert threshold before automatic circuit break. |

## Circuit Breaker

Manual circuit break:

1. The operator calls `PATCH /api/agents/:id` with `action: "circuit_break"`.
2. The API marks the agent inactive and circuit-broken.
3. The API creates a critical alert and event.
4. The API attempts onchain circuit-break sync.
5. The API writes an operator audit log.

Reset:

1. The operator submits an onchain reset from the wallet or lets the API attempt
   a relayer reset.
2. The dashboard calls `POST /api/agents/:id/reset-circuit`.
3. The API clears circuit-broken fields, marks the agent active, and writes an
   audit log.

## Alert Delivery

Alerts are created from critical events, guardrail violations, heartbeat misses,
and circuit breaker actions. Notification settings can enable webhook, Telegram,
and Farcaster delivery paths per agent.
