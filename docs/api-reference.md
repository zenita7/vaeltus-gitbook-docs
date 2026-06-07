# API Reference

The API lives in `apps/web/app/api` and is implemented as Next.js route
handlers.

## Authentication Modes

| Mode | Used By | Header Or Cookie |
| --- | --- | --- |
| Wallet session | Dashboard operator actions | HMAC-signed session cookie created after wallet signature verification. |
| Agent API key | SDK and agent runtime writes | `Authorization: Bearer <api-key>`. |
| Cron secret | Scheduled heartbeat monitor | Shared secret checked by the cron route. |

## Agent Registration

### `POST /api/agents/register`

Registers an agent for the currently signed-in wallet, or returns an existing
agent for that wallet with a fresh API key.

Auth: wallet session.

Request:

```json
{
  "name": "Trading Agent",
  "agentType": "trading",
  "description": "Monitors markets and submits guarded transactions.",
  "ownerAddress": "0x..."
}
```

Response includes:

| Field | Meaning |
| --- | --- |
| `agentId` | Supabase UUID used by API and SDK calls. |
| `onchainId` | `bytes32`-style identifier used by contracts. |
| `apiKey` | One-time API key. Store it immediately. |
| `onchainTxHash` | Registration tx hash when onchain sync succeeds. |
| `onchainSyncError` | Non-null when onchain sync was skipped or failed. |

## Agent Status And Controls

### `GET /api/agents/:id`

Fetches status and summary metrics for one agent.

Auth: matching agent API key or owner wallet session.

Response includes current status, heartbeat state, event count, unacknowledged
alert count, anomaly score, daily budget limit, guardrails, and creation time.

### `PATCH /api/agents/:id`

Updates dashboard-controlled lifecycle state.

Auth: owner wallet session.

Supported actions:

| Action | Result |
| --- | --- |
| `pause` | Marks the agent inactive. |
| `resume` | Marks the agent active and clears circuit-break flags. |
| `reset_circuit_break` | Clears circuit-break fields from the main agent route. |
| `circuit_break` | Marks inactive and circuit-broken, creates a critical alert and event, and attempts onchain sync. |

Request:

```json
{
  "action": "circuit_break",
  "reason": "Manual halt from operator dashboard."
}
```

## Events

### `POST /api/agents/:id/events`

Logs an agent event.

Auth: matching agent API key.

Rate limit: `120` events per `60` seconds per agent.

Request:

```json
{
  "eventType": "tool_call",
  "severity": "info",
  "data": {
    "tool": "uniswap_swap",
    "input": { "tokenIn": "ETH", "amount": "1.0" },
    "output": { "txHash": "0x..." },
    "success": true,
    "latencyMs": 420,
    "costUsd": 0.05
  }
}
```

Valid severities:

```text
info
warning
critical
```

Response:

```json
{
  "eventId": "uuid",
  "eventHash": "0x...",
  "anchored": false,
  "anchoredTx": null,
  "anchorError": null,
  "notificationResult": null,
  "guardRailViolation": null
}
```

Critical events and guardrail violations create alerts. They also attempt
onchain anchoring through the relayer configuration.

### `GET /api/agents/:id/events`

Lists recent events for one agent.

Auth: matching agent API key.

Query parameters:

| Parameter | Default | Notes |
| --- | --- | --- |
| `limit` | `50` | Clamped between `1` and `200`. |
| `type` | none | Filters by event type. |
| `severity` | none | Filters by severity. |
| `cursor` | none | Uses `created_at` cursor for pagination. |

## Heartbeats

### `POST /api/agents/:id/heartbeat`

Records liveness for an agent.

Auth: matching agent API key.

Rate limit: `30` heartbeats per `60` seconds per agent.

Request body is optional:

```json
{
  "metadata": {
    "activeTaskCount": 3,
    "memoryUsageMb": 256
  }
}
```

Paused or circuit-broken agents reject heartbeats with `403`.

## API Keys

### `POST /api/agents/:id/api-key`

Rotates the agent API key.

Auth: owner wallet session.

Rate limit: `5` rotations per hour per wallet and agent.

The new API key is returned once. Store it immediately.

### `DELETE /api/agents/:id/api-key`

Revokes the agent API key.

Auth: owner wallet session.

## Guardrails

### `GET /api/guardrails/:agentId`

Fetches current guardrail configuration.

Auth: matching agent API key or owner wallet session.

### `PUT /api/guardrails/:agentId`

Updates guardrails and attempts budget sync to the onchain governor.

Auth: owner wallet session.

Request can use camelCase or snake_case:

```json
{
  "maxSpendPerHour": 100,
  "maxSpendPerDay": 500,
  "maxTxPerMinute": 10,
  "allowedTools": ["swap", "bridge"],
  "blockedAddresses": ["0x0000000000000000000000000000000000000000"],
  "autoCircuitBreak": true,
  "criticalThreshold": 3
}
```

Blocked addresses must be valid lowercase or mixed-case EVM addresses.

## Alerts

### `PATCH /api/alerts`

Acknowledges all active alerts for the signed-in owner. Can optionally scope to
one agent.

Auth: owner wallet session.

Request:

```json
{
  "agentId": "uuid"
}
```

### `PATCH /api/alerts/:id`

Acknowledges or reopens one alert.

Auth: owner wallet session.

Request:

```json
{
  "acknowledged": true
}
```

## Notification Settings

### `GET /api/notification-settings`

Lists notification settings for all agents owned by the signed-in wallet.

Auth: owner wallet session.

### `PUT /api/notification-settings`

Creates or updates settings for one agent.

Auth: owner wallet session.

Request:

```json
{
  "agentId": "uuid",
  "webhookUrl": "https://example.com/webhook",
  "telegramBotToken": "token",
  "telegramChatId": "chat-id",
  "farcasterFid": "123",
  "farcasterSignerUuid": "uuid",
  "notifyOnCircuitBreak": true,
  "notifyOnHeartbeatMiss": true,
  "notifyOnBudgetWarning": true,
  "notifyOnCritical": true
}
```

## Auth

| Method | Route | Purpose |
| --- | --- | --- |
| `POST` | `/api/auth/nonce` | Create a wallet challenge and nonce cookie. |
| `POST` | `/api/auth/verify` | Verify a wallet signature and create a session cookie. |
| `GET` | `/api/auth/session` | Return the active wallet session. |
| `POST` | `/api/auth/logout` | Clear the active session. |

## Dashboard, Audit, Cron, And Delivery

| Method | Route | Purpose |
| --- | --- | --- |
| `GET` | `/api/dashboard` | Dashboard snapshot for the signed-in owner. |
| `GET` | `/api/audit-log` | Operator audit log for the signed-in owner. |
| `POST` | `/api/cron/heartbeat-monitor` | Sweep stale heartbeats and auto circuit-break when configured. |
| `POST` | `/api/alerts/notify` | Deliver pending alerts through configured channels. |
| `POST` | `/api/agents/:id/reset-circuit` | Reset circuit breaker state after onchain reset flow. |
| `POST` | `/api/agents/:id/sync-onchain` | Sync agent state to onchain contracts. |
