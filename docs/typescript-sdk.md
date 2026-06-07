# TypeScript SDK

The TypeScript SDK is published as `@vaeltus/sdk` and lives in
`packages/sdk`.

It sends agent events and heartbeats to the VAELTUS API while applying local
guardrail checks before risky actions are shipped.

## Install

```bash
npm install @vaeltus/sdk
```

This installs the client SDK only. It does not install the dashboard, API
routes, Supabase schema, contracts, examples, or monorepo build outputs.

## Basic Usage

```ts
import { Vaeltus } from '@vaeltus/sdk';

const vaeltus = new Vaeltus({
  agentId: process.env.VAELTUS_AGENT_ID!,
  apiKey: process.env.VAELTUS_API_KEY!,
  baseUrl: process.env.VAELTUS_BASE_URL ?? 'https://vaeltus.xyz',
  guardrails: {
    maxSpendPerHourUsd: 100,
    maxSpendPerDayUsd: 500,
    maxTxPerMinute: 10,
    allowedTools: ['swap', 'bridge', 'transfer'],
    blockedAddresses: ['0x0000000000000000000000000000000000000000'],
  },
});

await vaeltus.connect();

await vaeltus.logToolCall(
  'uniswap_swap',
  { tokenIn: 'ETH', amount: '1.0' },
  { txHash: '0x...' },
  { success: true, latencyMs: 420, costUsd: 0.05 }
);

await vaeltus.sendHeartbeat({
  status: 'alive',
  metadata: { activeTaskCount: 3 },
});

await vaeltus.disconnect();
```

## Configuration

| Field | Type | Default |
| --- | --- | --- |
| `agentId` | `string` | Required |
| `apiKey` | `string` | Required |
| `baseUrl` | `string` | `https://vaeltus.xyz` |
| `guardrails` | `GuardrailConfig` | `{}` |
| `heartbeatInterval` | `number` | `30000` ms |
| `debug` | `boolean` | `false` |
| `failMode` | `'open' \| 'closed'` | `'open'` |
| `maxRetries` | `number` | `3` |
| `retryDelayMs` | `number` | `500` |

`baseUrl` must use HTTPS, except for local development hosts:

```text
localhost
127.0.0.1
::1
```

## Guardrails

| Field | Meaning |
| --- | --- |
| `maxSpendPerHourUsd` | Maximum locally recorded spend per hour. |
| `maxSpendPerDayUsd` | Maximum locally recorded spend per day. |
| `allowedTools` | Tool allowlist. Empty means no tool allowlist restriction. |
| `blockedAddresses` | Destination addresses rejected before logging. |
| `maxTxPerMinute` | Local transaction rate limit. |
| `enabled` | Mirrors `BudgetGovernor.sol` budget enablement. |

Local guardrail violations return an `EventResult` with
`guardRailViolation`. The SDK also attempts a best-effort critical event log so
the dashboard can show the incident.

## Methods

| Method | Purpose |
| --- | --- |
| `Vaeltus.register(payload, config?)` | Register an agent through `/api/agents/register`. |
| `connect()` | Verify agent credentials and start heartbeat loop. |
| `disconnect()` | Stop heartbeat loop and send final degraded heartbeat. |
| `updateGuardrails(config)` | Update local SDK guardrail config. |
| `logEvent(event)` | Send any event after local guardrail checks. |
| `logToolCall(tool, input, output?, opts?)` | Convenience helper for `tool_call` events. |
| `logTransaction(txHash, chain, from, opts?)` | Convenience helper for `transaction` events. |
| `logError(message, context?)` | Log a critical error event. |
| `trace(name, fn)` | Time an async span and log a `reasoning` event. |
| `checkBudget(amount)` | Check and record spend locally. |
| `checkToolAllowed(tool)` | Check tool allowlist locally. |
| `checkAddress(address)` | Check blocklist locally. |
| `startHeartbeat()` | Start periodic heartbeat loop. |
| `stopHeartbeat()` | Stop periodic heartbeat loop. |
| `sendHeartbeat(payload?)` | Send one heartbeat. |

## Event Types

```ts
type EventType =
  | 'tool_call'
  | 'transaction'
  | 'reasoning'
  | 'error'
  | 'heartbeat'
  | 'custom';
```

Severity values:

```ts
type Severity = 'info' | 'warning' | 'critical';
```

## Register An Agent

```ts
import { Vaeltus } from '@vaeltus/sdk';

const agent = await Vaeltus.register(
  {
    name: 'Trading Agent',
    agentType: 'trading',
    description: 'Monitors markets and submits guarded transactions.',
    ownerAddress: process.env.AGENT_OWNER_ADDRESS!,
  },
  {
    baseUrl: process.env.VAELTUS_BASE_URL,
  }
);

console.log(agent.agentId, agent.apiKey, agent.onchainId);
```

Registration requires the API route to accept the request. In dashboard flows,
the web app expects a wallet session. For agent-side operation, prefer creating
the agent through the dashboard and storing the returned API key.

## Local Development

```bash
npm run build --workspace=packages/sdk
npm run test --workspace=packages/sdk
npm publish --workspace=packages/sdk --access public --dry-run
```

Install a local tarball in another project:

```bash
npm run build --workspace=packages/sdk
npm pack --workspace=packages/sdk
```
