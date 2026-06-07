# Agent Simulator

The agent simulator lives in `apps/agent`. It is an autonomous Base Sepolia demo
runner that uses OpenRouter for decisions, `viem` for onchain actions, and
`@vaeltus/sdk` for observability.

## Purpose

Use the simulator to exercise the full VAELTUS path:

1. Validate env and network connectivity.
2. Register or reconnect an agent.
3. Configure guardrails.
4. Run a reason, decide, execute, observe, and report loop.
5. Emit events and heartbeats through the SDK.
6. Stop when signaled, circuit-broken, or the max iteration count is reached.

## Requirements

| Requirement | Notes |
| --- | --- |
| Node.js 18+ | Workspace scripts run from npm. Node 20 is recommended for the repo. |
| OpenRouter API key | Used for LLM decision-making. |
| Funded Base Sepolia wallet | Used for real testnet transactions. |
| VAELTUS API | Usually `http://localhost:3000` in local development. |
| Deployed contracts | Registry, event anchor, and budget governor addresses. |

## Setup

```bash
cp apps/agent/.env.example apps/agent/.env.local
npm install
npm run build --workspace=packages/sdk
```

Then fill `apps/agent/.env.local`.

Important variables:

| Variable | Required | Default |
| --- | --- | --- |
| `OPENROUTER_API_KEY` | Yes | none |
| `DEPLOYER_PRIVATE_KEY` | Yes | none |
| `NEXT_PUBLIC_REGISTRY_ADDRESS` | Yes | none |
| `NEXT_PUBLIC_EVENT_ANCHOR_ADDRESS` | Yes | none |
| `NEXT_PUBLIC_BUDGET_GOVERNOR_ADDRESS` | Yes | none |
| `VAELTUS_BASE_URL` | No | `http://localhost:3000` |
| `BASE_SEPOLIA_RPC_URL` | No | `https://sepolia.base.org` |
| `OPENROUTER_MODEL` | No | `anthropic/claude-sonnet-4` |
| `LOOP_INTERVAL_MS` | No | `15000` |
| `MAX_ITERATIONS` | No | `50` |
| `MAX_GAS_ETH` | No | `0.01` |
| `DEBUG` | No | `false` |

## Run

From the workspace root:

```bash
npm run start --workspace=apps/agent
```

Or from `apps/agent`:

```bash
npm start
```

## State File

On first run, the simulator registers and stores credentials in:

```text
apps/agent/.agent-state.json
```

The file is local runtime state and should not be committed. Delete it to force
a fresh registration.

## Shutdown

Use `Ctrl+C` or send `SIGTERM`. The simulator completes the current iteration,
sends a final degraded heartbeat, disconnects the SDK, prints a summary, and
exits.

If graceful shutdown exceeds 10 seconds, it exits with code `1`.

## Tests

```bash
npm test --workspace=apps/agent
npm run test:unit --workspace=apps/agent
npm run test:properties --workspace=apps/agent
```
