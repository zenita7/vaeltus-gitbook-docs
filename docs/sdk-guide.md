# VAELTUS SDK Guide

The TypeScript SDK lets an agent send events and heartbeats to the VAELTUS API while enforcing simple client-side guardrails before risky actions are shipped.

## Install

```bash
npm install @vaeltus/sdk
```

During local development in this monorepo:

```bash
npm run build --workspace=packages/sdk
```

## Configure

```ts
import { Vaeltus } from '@vaeltus/sdk';

const vaeltus = new Vaeltus({
  agentId: process.env.VAELTUS_AGENT_ID!,
  apiKey: process.env.VAELTUS_API_KEY!,
  baseUrl: process.env.VAELTUS_BASE_URL ?? 'http://localhost:3000',
  debug: true,
  guardrails: {
    maxSpendPerHourUsd: 100,
    maxSpendPerDayUsd: 500,
    maxTxPerMinute: 10,
    allowedTools: ['uniswap_swap', 'check_liquidity'],
    blockedAddresses: ['0x0000000000000000000000000000000000000000'],
  },
});
```

## Log Events

```ts
await vaeltus.logEvent({
  eventType: 'tool_call',
  severity: 'info',
  data: {
    tool: 'uniswap_swap',
    success: true,
    latencyMs: 420,
    costUsd: 0.05,
  },
});
```

Transaction example:

```ts
await vaeltus.logEvent({
  eventType: 'transaction',
  severity: 'info',
  data: {
    txHash: '0x...',
    chain: 'base-sepolia',
    from: '0x...',
    to: '0x...',
    valueUsd: 12.5,
    success: true,
  },
});
```

## Heartbeats

```ts
vaeltus.startHeartbeat();

await vaeltus.sendHeartbeat({
  status: 'alive',
  metadata: {
    activeTaskCount: 3,
    memoryUsageMb: 256,
  },
});

vaeltus.stopHeartbeat();
```

## Guardrail Behavior

The SDK checks:

- Allowed tool whitelist.
- Blocked destination addresses.
- Hourly and daily spend limits.
- Transaction rate limits.

When a local guardrail trips, `logEvent` returns an `EventResult` with `guardRailViolation` and attempts to send a critical violation event to the API.

## API Contract

All SDK requests use:

```http
Authorization: Bearer <agent-api-key>
Content-Type: application/json
```

Core endpoints:

- `POST /api/agents/:id/events`
- `POST /api/agents/:id/heartbeat`
- `GET /api/agents/:id`
- `GET /api/agents/:id/events`

