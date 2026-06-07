# Smart Contracts

The Solidity contracts live in `contracts/src` and are tested with Foundry.
They provide the onchain audit and enforcement layer for VAELTUS.

## Contracts

| Contract | Role |
| --- | --- |
| `VaeltusRegistry.sol` | Registers agents, tracks owner metadata, active state, circuit-break state, and event anchor counts. |
| `BudgetGovernor.sol` | Stores budget limits and spend trackers for registered agents. |
| `EventAnchor.sol` | Anchors event hashes and verifies whether hashes have been anchored. |
| `CircuitBreaker.sol` | Provides a halt and recovery helper around registry circuit-break state. |

## VaeltusRegistry

Core roles:

| Role | Purpose |
| --- | --- |
| `DEFAULT_ADMIN_ROLE` | Grants and revokes roles. |
| `WATCHER_ROLE` | Can pause or circuit-break agents through watcher paths. |
| `ANCHOR_ROLE` | Can increment registry event anchor counts. |
| `REGISTRAR_ROLE` | Can register agents for a verified owner through a trusted relayer path. |

Main functions:

| Function | Purpose |
| --- | --- |
| `registerAgent(name, agentType, description)` | Self-register an agent from `msg.sender`. |
| `registerAgentFor(owner, agentId, name, agentType, description)` | Relayer registration after offchain wallet verification. |
| `pauseAgent(agentId)` | Pause an active agent. |
| `resumeAgent(agentId)` | Resume a paused, non-circuit-broken agent. |
| `circuitBreak(agentId, reason)` | Mark an agent circuit-broken and inactive. |
| `resetCircuit(agentId)` | Clear circuit-break state and reactivate the agent. |
| `anchorEvent(agentId, eventHash)` | Increment event anchor count after an event is anchored. |
| `getAgent(agentId)` | Return agent metadata. |
| `getOwnerAgents(owner)` | Return agent IDs owned by an address. |
| `getAllAgentIds()` | Return all registered agent IDs. |

## BudgetGovernor

Budget values use 6-decimal USD format:

```text
1.00 USD = 1_000_000
```

Budget config:

| Field | Meaning |
| --- | --- |
| `maxPerTx` | Maximum single spend attempt. |
| `maxPerHour` | Maximum hourly spend. |
| `maxPerDay` | Maximum daily spend. |
| `enabled` | Whether budget enforcement is active. |

Main functions:

| Function | Purpose |
| --- | --- |
| `setBudget(agentId, config)` | Owner-managed budget update. |
| `setBudgetFor(agentId, config)` | Relayer-managed budget update through `BUDGET_MANAGER_ROLE`. |
| `checkAndRecord(agentId, amountUsd6)` | Validate and record a spend attempt. |
| `getSpendSnapshot(agentId)` | Read hourly and daily usage and limits. |

## EventAnchor

`EventAnchor` maps an `agentId` and `eventHash` to the timestamp when the event
was anchored.

Main functions:

| Function | Purpose |
| --- | --- |
| `anchor(agentId, eventHash)` | Anchor one event hash. |
| `anchorBatch(agentId, eventHashes)` | Anchor multiple event hashes. |
| `verify(agentId, eventHash)` | Return existence and timestamp for one event hash. |
| `verifyBatch(agentId, eventHashes)` | Verify multiple hashes. |

## CircuitBreaker

`CircuitBreaker` wraps registry circuit-break state and records halt timestamps.
It enforces a one-hour minimum recovery delay before `recover()` can be called.

Main functions:

| Function | Purpose |
| --- | --- |
| `halt(agentId, reason)` | Halt an agent and call registry `circuitBreak`. |
| `recover(agentId)` | Mark the external breaker as recovered after the delay. |
| `isHalted(agentId)` | Check breaker halt state. |
| `canRecover(agentId)` | Return recovery eligibility and seconds remaining. |

## Run Tests

```bash
cd contracts
forge test -vvv
```

## Deploy

```bash
cd contracts
forge script script/Deploy.s.sol \
  --rpc-url "$BASE_SEPOLIA_RPC_URL" \
  --broadcast \
  --verify \
  --etherscan-api-key "$BASESCAN_API_KEY"
```

Use a dedicated deployer wallet and deliberately assign relayer roles after
deployment.
