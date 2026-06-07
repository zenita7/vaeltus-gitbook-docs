# VAELTUS Protocol — Product Requirements Document (PRD)

> **Onchain AI Agent Observability & Guardrails Protocol on Base**
> **Token: $VLTS**

---

## 1. Vision & Problem Statement

### Vision
VAELTUS menjadi **infrastruktur standar** untuk monitoring, auditing, dan securing AI agents onchain — dimulai dari Base ecosystem.

### Problem
| Problem | Current State | VAELTUS Solution |
|---------|--------------|-------------------|
| Silent Failures | Agent loop/hallucinate tanpa ada yang tahu | Real-time anomaly detection + circuit breaker |
| Black Box Decisions | Tidak bisa trace "kenapa" agent buat keputusan | Immutable reasoning trace onchain |
| Cost Spirals | Agent retry tanpa kontrol, biaya meledak | Onchain budget governor |
| No Accountability | Tidak ada audit trail yang tamper-proof | Blockchain-anchored attestation layer |
| Over-permissioning | Agent dapat akses terlalu luas | Granular permission policies via smart contract |

> [!NOTE]
> **Branding:** Project ini resmi menggunakan nama **VAELTUS** dengan token **$VLTS**.

### Why Now?
- **EU AI Act 2026** → Wajibkan audit trail untuk autonomous systems
- **Base menjadi hub AI agent** → Virtuals, Based Agent, AIXBT semua di sini
- **x402 + MCP adoption** → Agent economy growing exponentially
- **Zero onchain competitor** → Semua observability tools (Arize, Galileo) masih Web2

---

## 2. Target Users

### Primary
| Persona | Description | Pain Point |
|---------|-------------|------------|
| **Agent Builders** | Developer yang build AI agents di Base (pakai Virtuals, ElizaOS, dll) | Tidak bisa monitor agent behavior di production |
| **DeFi Agent Operators** | Operator yang run trading/yield agents | Takut agent lose funds tanpa warning |
| **Agent Token Holders** | Holder token agent di Virtuals | Tidak ada transparency soal agent performance |

### Secondary
| Persona | Description | Pain Point |
|---------|-------------|------------|
| **DAOs** | Organization yang deploy agents untuk governance | Need compliance & audit trail |
| **Enterprises** | Company exploring agent automation | Regulatory compliance (EU AI Act) |

---

## 3. Feature Specification

### MVP (V1) — "The Watcher"
> **Goal:** Prove core value — monitoring + alerting untuk AI agents

| Feature | Description | Priority |
|---------|-------------|----------|
| **Agent Registry** | Register agent dengan metadata (name, owner, type, chain) | P0 |
| **Heartbeat Monitor** | Agent send periodic heartbeat; alert kalau miss | P0 |
| **Event Logger** | Log key agent events (tool calls, transactions, errors) ke Base | P0 |
| **Budget Governor** | Set max spend limit per agent per time window | P0 |
| **Alert System** | Webhook/Telegram/Farcaster alerts saat anomali | P0 |
| **Dashboard** | Web dashboard untuk lihat agent status & logs | P0 |
| **Circuit Breaker** | Emergency halt agent via smart contract | P1 |

### V2 — "The Guardian"
> **Goal:** Deep observability + automated guardrails

| Feature | Description | Priority |
|---------|-------------|----------|
| **Reasoning Trace** | Full trace dari setiap reasoning step (hashed onchain) | P0 |
| **Behavioral Guardrails Engine** | Define rules: allowed tools, max frequency, restricted addresses | P0 |
| **Anomaly Detection** | ML-based pattern recognition untuk unusual behavior | P1 |
| **Multi-Agent Monitoring** | Monitor agent swarms & A2A interactions | P1 |
| **Compliance Report Generator** | Auto-generate audit reports (EU AI Act format) | P1 |

### V3 — "The Network"
> **Goal:** Decentralized watcher network for anomaly detection, verification, and escalation

| Feature | Description | Priority |
|---------|-------------|----------|
| **Watcher Network** | Decentralized validators yang monitor agents, verify anomalies, dan emit escalation signals | P0 |
| **Staking & Slashing** | Watchers stake $VLTS untuk kualitas laporan; inaccurate reports dan bad behavior can be penalized | P0 |
| **Reputation Integration** | Agent reputation scores dari monitoring data | P1 |
| **Cross-chain Monitoring** | Expand ke chain lain (Ethereum, Arbitrum, dll) | P2 |
| **Insurance Pool** | Stake $VLTS untuk cover losses dari agent failures | P2 |

> Watchers do not directly halt agents. Enforcement tetap dilakukan oleh guardrails, budget governor, dan circuit breaker; watcher network hanya memberi signal, evidence, dan economic incentives.

---

## 4. Token Launch Strategy

### Platform: Clanker (Primary) + Virtuals Pegasus (Secondary)

#### Why Clanker?
- ✅ Instant deployment, no upfront cost
- ✅ Native Farcaster integration (Base community lives here)
- ✅ Auto liquidity pool on Uniswap V3
- ✅ Perfect untuk initial momentum & community building

#### Why Virtuals Pegasus (Phase 2)?
- ✅ Positions VAELTUS sebagai part of Virtuals ecosystem
- ✅ Access ke audience agent builders
- ✅ Bonding curve → graduation → LP lock (10 tahun)
- ✅ 70% fee ke creator

### Token: `$VLTS`

#### Token Specs
| Parameter | Value |
|-----------|-------|
| Name | VAELTUS |
| Symbol | $VLTS |
| Standard | ERC-20 |
| Chain | Base (Chain ID: 8453) |
| Total Supply | 1,000,000,000 (1B) |
| Decimals | 18 |

#### Launch Sequence
```
Day 0:  Deploy $VLTS via Clanker on Farcaster
        → Auto Uniswap V3 pool created
        → Announce on Farcaster, X, Telegram
        
Week 1: Build initial community
        → Share VAELTUS vision & roadmap
        → Engage Base/Virtuals community
        → Start building MVP (publicly)
        
Week 2-3: Launch landing page + litepaper
          → Show progress on MVP
          → Early tester access program
          
Month 1-2: MVP launch (testnet)
           → Integrate with 2-3 Virtuals agents
           → Dashboard live
           
Month 2-3: MVP launch (mainnet)
           → Token utility activated (pay for monitoring)
           → Staking program begins
           
Month 3+: Expand features + partnerships
          → Virtuals Pegasus listing (optional)
          → More agent integrations
```

#### Token Utility Table
| Utility | Phase | Mechanism |
|---------|-------|-----------|
| **Monitoring Fees** | MVP | Pay $VLTS to monitor your agents |
| **Premium Features** | MVP | Stake $VLTS for advanced dashboards & alerts |
| **Governance** | V2 | Vote on guardrail templates & protocol params |
| **Watcher Staking** | V3 | Stake $VLTS to operate as a watcher node and submit verified anomaly reports |
| **Watcher Rewards** | V3 | Earn $VLTS for accurate anomaly detection and valid escalation signals |
| **Slashing** | V3 | Poor-quality reports, misconduct, or misbehavior can lose staked $VLTS |
| **Insurance Pool** | V3 | Stake to underwrite losses from agent failures and incident recovery |

> [!IMPORTANT]
> **Strategy: FREE FIRST** — MVP phase tidak ada paywall. Semua fitur gratis.
> Token utility di-activate setelah ada traction & product-market fit.
> Watcher network memperkuat detection dan escalation; enforcement tetap berada di guardrails dan circuit breaker.
> Revenue awal 100% dari trading fees (70% via Clanker).

#### Initial Distribution (via Clanker)
> [!NOTE]
> Clanker does a simple fair launch — no pre-allocation. All tokens go to the liquidity pool.
> Post-launch, the creator earns 70% of trading fees (1% per trade) automatically.

**Post-Launch Treasury Building Strategy:**
| Source | Strategy |
|--------|----------|
| Trading Fees | 70% of Clanker LP fees → project treasury |
| Protocol Revenue | Monitoring fees paid in $VLTS (Phase 2+) |
| Buybacks | Use protocol revenue untuk buyback $VLTS from market |
| Grants | Apply for Base ecosystem grants |

---

## 5. System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    VAELTUS PROTOCOL                      │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────┐    ┌──────────────┐    ┌────────────┐ │
│  │  VAELTUS      │    │  VAELTUS      │    │ VAELTUS    │ │
│  │  SDK          │    │  DASHBOARD    │    │ CONTRACTS  │ │
│  │  (Agent-side) │    │  (Frontend)   │    │ (Base)     │ │
│  └──────┬───────┘    └──────┬───────┘    └─────┬──────┘ │
│         │                   │                   │        │
│  ┌──────▼───────────────────▼───────────────────▼──────┐ │
│  │              VAELTUS BACKEND API                     │ │
│  │  ┌─────────┐ ┌──────────┐ ┌───────────┐ ┌────────┐ │ │
│  │  │ Event   │ │ Anomaly  │ │ Alert     │ │ Budget │ │ │
│  │  │ Logger  │ │ Detector │ │ Manager   │ │ Guard  │ │ │
│  │  └─────────┘ └──────────┘ └───────────┘ └────────┘ │ │
│  └─────────────────────┬───────────────────────────────┘ │
│                        │                                  │
│  ┌─────────────────────▼───────────────────────────────┐ │
│  │              DATA LAYER                              │ │
│  │  ┌──────────┐  ┌───────────┐  ┌──────────────────┐ │ │
│  │  │ Supabase │  │ Base      │  │ IPFS/Arweave     │ │ │
│  │  │ (Events) │  │ (Hashes)  │  │ (Full Traces)    │ │ │
│  │  └──────────┘  └───────────┘  └──────────────────┘ │ │
│  └─────────────────────────────────────────────────────┘ │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Data Flow

```
AI Agent (ElizaOS / Virtuals / Custom)
    │
    ├── 1. Install VAELTUS SDK
    │
    ├── 2. Register Agent on VAELTUS Registry (onchain)
    │
    ├── 3. Agent runs tasks...
    │       │
    │       ├── Every tool call → SDK sends event to VAELTUS API
    │       ├── Every transaction → SDK sends tx hash + context
    │       ├── Every N seconds → SDK sends heartbeat
    │       └── Every reasoning step → SDK sends hashed trace
    │
    ├── 4. VAELTUS API processes events:
    │       │
    │       ├── Store in Supabase (queryable)
    │       ├── Hash critical events → anchor to Base
    │       ├── Check against guardrail rules
    │       ├── Run anomaly detection
    │       └── Check budget limits
    │
    └── 5. If anomaly detected:
            │
            ├── Send alert (Telegram / Farcaster / Webhook)
            ├── Log violation onchain
            └── (Optional) Trigger circuit breaker → halt agent
```

### Component Breakdown

#### A. VAELTUS SDK (TypeScript/Python)
Lightweight library yang di-install di agent codebase.

```typescript
// Usage example
import { Vaeltus } from '@vaeltus/sdk';

const vaeltus = new Vaeltus({
  agentId: '0x...',     // Agent's onchain identity
  apiKey: 'vlts_...',   // API key dari dashboard
  guardRails: {
    maxSpendPerHour: 100,    // USDC
    allowedTools: ['swap', 'bridge', 'transfer'],
    blockedAddresses: ['0x...'],
    maxTransactionsPerMinute: 10,
  }
});

// Wrap agent execution
vaeltus.trace('reasoning', async () => {
  // Agent reasoning logic here
  const decision = await agent.think(context);
  return decision;
});

// Log events
vaeltus.logEvent({
  type: 'tool_call',
  tool: 'uniswap_swap',
  input: { tokenIn: 'ETH', tokenOut: 'USDC', amount: '1.0' },
  output: { success: true, txHash: '0x...' }
});

// Heartbeat (auto)
vaeltus.startHeartbeat(interval: 30000); // every 30s
```

#### B. VAELTUS Smart Contracts (Solidity — Base)

| Contract | Purpose |
|----------|---------|
| `VaeltusRegistry.sol` | Register & manage agents onchain |
| `BudgetGovernor.sol` | Enforce spending limits per agent |
| `CircuitBreaker.sol` | Emergency halt mechanism |
| `EventAnchor.sol` | Anchor event hashes onchain |
| `VaeltusToken.sol` | $VLTS ERC-20 token (backup jika tidak pakai Clanker) |

#### C. VAELTUS Backend API

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/agents/register` | POST | Register new agent |
| `/agents/:id/events` | POST | Log agent events |
| `/agents/:id/heartbeat` | POST | Agent heartbeat |
| `/agents/:id/status` | GET | Get agent status |
| `/agents/:id/events` | GET | Get agent event history |
| `/agents/:id/alerts` | GET | Get agent alerts |
| `/guardrails/:agentId` | GET/PUT | Get/update guardrail config |
| `/dashboard/overview` | GET | Dashboard aggregate data |

#### D. VAELTUS Dashboard (Web App)

| Page | Description |
|------|-------------|
| **Overview** | All registered agents, status grid, alert summary |
| **Agent Detail** | Single agent view: events timeline, trace viewer, budget usage |
| **Guardrails Config** | Visual editor untuk set guardrail rules |
| **Alerts** | Alert history, notification settings |
| **Analytics** | Charts: events over time, anomaly frequency, cost tracking |

---

## 6. User Flows

### Flow 1: Agent Builder Registers Agent
```
1. Builder visits vaeltus.xyz (or configured domain)
2. Connect wallet (Coinbase Wallet / MetaMask)
3. Click "Register Agent"
4. Fill form: agent name, type, description
5. Sign transaction → Agent registered onchain (VaeltusRegistry.sol)
6. Get API key for SDK integration
7. Install SDK in agent codebase
8. Agent starts sending events → Dashboard populates
```

### Flow 2: Set Budget Guardrail
```
1. Builder opens Agent Detail page
2. Click "Guardrails" tab
3. Set: "Max spend: 50 USDC per hour"
4. Set: "Alert me at 80% usage"
5. Sign transaction → BudgetGovernor.sol updated
6. Agent approaches limit → Alert sent via Telegram
7. Agent exceeds limit → CircuitBreaker triggered → Agent halted
```

### Flow 3: Anomaly Detected
```
1. Agent suddenly doing 100x normal transaction frequency
2. VAELTUS Anomaly Detector flags it
3. Alert sent to owner via configured channels
4. Event logged onchain with severity level
5. If severity >= CRITICAL → Auto circuit break
6. Owner reviews trace in dashboard
7. Owner decides: resume or kill agent
```

---

## 7. Technical Stack

### Frontend
| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Framework | **Next.js 15** (App Router) | SSR, API routes, best DX |
| Styling | **Vanilla CSS** + CSS Modules | Maximum control, VAELTUS brand system |
| Charts | **Recharts** or **Chart.js** | Agent analytics visualization |
| Wallet | **wagmi v2** + **viem** | Base wallet integration |
| State | **Zustand** | Lightweight global state |
| Real-time | **Supabase Realtime** | Live event streaming |

### Backend
| Layer | Technology | Rationale |
|-------|-----------|-----------|
| API | **Next.js API Routes** | Same deployment, simplicity |
| Database | **Supabase (PostgreSQL)** | Free tier, realtime, auth |
| Onchain Data | **Viem** + Base RPC | Read/write to Base contracts |
| Queue | **Supabase Edge Functions** | Process events async |
| Storage | **IPFS (web3.storage)** | Full trace storage (off-chain) |

### Smart Contracts
| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Language | **Solidity 0.8.28** | Latest stable |
| Framework | **Foundry** | Fast, fuzzing, industry standard |
| Library | **OpenZeppelin 5.x** | Audited ERC-20, AccessControl |
| Deploy | **Foundry scripts** | Deterministic deployment |
| Verify | **BaseScan** | Contract transparency |

### SDK
| Layer | Technology | Rationale |
|-------|-----------|-----------|
| TypeScript SDK | **TypeScript** + **tsup** | Most agent frameworks are TS |
| Python SDK | **Python** + **httpx** | ElizaOS & some agents use Python |
| Package | **npm** / **pip** | Standard distribution |

---

## 8. Smart Contract Design

### VaeltusRegistry.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

import "@openzeppelin/contracts/access/AccessControl.sol";

contract VaeltusRegistry is AccessControl {
    
    struct Agent {
        address owner;
        string name;
        string agentType;        // "defi", "social", "trading", etc.
        uint256 registeredAt;
        bool isActive;
        bool isCircuitBroken;
        uint256 budgetLimit;     // in wei (USDC decimals)
        uint256 budgetSpent;     // current period spend
        uint256 budgetPeriod;    // reset interval in seconds
        uint256 lastBudgetReset;
    }
    
    mapping(bytes32 => Agent) public agents;          // agentId => Agent
    mapping(address => bytes32[]) public ownerAgents;  // owner => agentIds
    
    event AgentRegistered(bytes32 indexed agentId, address indexed owner, string name);
    event AgentPaused(bytes32 indexed agentId, string reason);
    event AgentResumed(bytes32 indexed agentId);
    event BudgetExceeded(bytes32 indexed agentId, uint256 spent, uint256 limit);
    event EventAnchored(bytes32 indexed agentId, bytes32 eventHash, uint256 timestamp);
    
    function registerAgent(string memory name, string memory agentType) external returns (bytes32);
    function pauseAgent(bytes32 agentId) external;
    function resumeAgent(bytes32 agentId) external;
    function setBudget(bytes32 agentId, uint256 limit, uint256 period) external;
    function recordSpend(bytes32 agentId, uint256 amount) external;
    function anchorEvent(bytes32 agentId, bytes32 eventHash) external;
    function circuitBreak(bytes32 agentId, string memory reason) external;
}
```

### BudgetGovernor.sol
```solidity
contract BudgetGovernor {
    
    struct BudgetConfig {
        uint256 maxPerTransaction;  // max single tx amount
        uint256 maxPerHour;         // max per hour
        uint256 maxPerDay;          // max per day
        address[] allowedTokens;    // tokens agent can interact with
    }
    
    mapping(bytes32 => BudgetConfig) public budgets;
    mapping(bytes32 => uint256) public hourlySpend;
    mapping(bytes32 => uint256) public dailySpend;
    
    function checkAndRecord(bytes32 agentId, uint256 amount, address token) 
        external returns (bool allowed);
    function setBudgetConfig(bytes32 agentId, BudgetConfig memory config) external;
    function resetPeriod(bytes32 agentId) external;
}
```

---

## 9. Database Schema (Supabase)

```sql
-- Agents table
CREATE TABLE agents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    onchain_id VARCHAR(66) NOT NULL UNIQUE,  -- bytes32 hex
    owner_address VARCHAR(42) NOT NULL,
    name VARCHAR(255) NOT NULL,
    agent_type VARCHAR(50),
    description TEXT,
    api_key VARCHAR(64) NOT NULL UNIQUE,
    is_active BOOLEAN DEFAULT true,
    is_circuit_broken BOOLEAN DEFAULT false,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Events table (main log)
CREATE TABLE events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    agent_id UUID REFERENCES agents(id),
    event_type VARCHAR(50) NOT NULL,  -- 'tool_call', 'transaction', 'reasoning', 'error', 'heartbeat'
    severity VARCHAR(20) DEFAULT 'info',  -- 'info', 'warning', 'critical'
    data JSONB NOT NULL,
    event_hash VARCHAR(66),            -- keccak256 hash
    anchored_tx VARCHAR(66),           -- Base tx hash (if anchored onchain)
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Guardrails table
CREATE TABLE guardrails (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    agent_id UUID REFERENCES agents(id) UNIQUE,
    max_spend_per_hour NUMERIC,
    max_spend_per_day NUMERIC,
    max_tx_per_minute INTEGER,
    allowed_tools TEXT[],
    blocked_addresses TEXT[],
    auto_circuit_break BOOLEAN DEFAULT false,
    critical_threshold INTEGER DEFAULT 3,  -- # of critical events before auto-break
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Alerts table
CREATE TABLE alerts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    agent_id UUID REFERENCES agents(id),
    alert_type VARCHAR(50) NOT NULL,  -- 'budget_warning', 'anomaly', 'heartbeat_miss', 'circuit_break'
    severity VARCHAR(20) NOT NULL,
    message TEXT NOT NULL,
    acknowledged BOOLEAN DEFAULT false,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Heartbeats table
CREATE TABLE heartbeats (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    agent_id UUID REFERENCES agents(id),
    status VARCHAR(20) DEFAULT 'alive',
    metadata JSONB,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Create indexes
CREATE INDEX idx_events_agent_id ON events(agent_id);
CREATE INDEX idx_events_type ON events(event_type);
CREATE INDEX idx_events_created ON events(created_at DESC);
CREATE INDEX idx_alerts_agent_id ON alerts(agent_id);
CREATE INDEX idx_heartbeats_agent ON heartbeats(agent_id, created_at DESC);
```

---

## 10. API Specification

### Authentication
Semua API calls harus include header:
```
Authorization: Bearer vlts_<api_key>
```

### Core Endpoints

#### POST `/api/agents/register`
```json
{
  "name": "MyDeFiAgent",
  "agentType": "defi",
  "description": "Automated yield farming agent",
  "ownerAddress": "0x..."
}
// Response: { agentId, apiKey, onchainId }
```

#### POST `/api/agents/:id/events`
```json
{
  "eventType": "tool_call",
  "severity": "info",
  "data": {
    "tool": "uniswap_swap",
    "input": { "tokenIn": "ETH", "amount": "1.0" },
    "output": { "success": true, "txHash": "0x..." },
    "latencyMs": 1200,
    "costUsd": 0.05
  }
}
// Response: { eventId, eventHash }
```

#### POST `/api/agents/:id/heartbeat`
```json
{
  "status": "alive",
  "metadata": {
    "memoryUsageMb": 256,
    "activeTaskCount": 3,
    "lastToolCall": "2026-05-22T08:00:00Z"
  }
}
```

#### GET `/api/agents/:id/status`
```json
// Response
{
  "agentId": "...",
  "name": "MyDeFiAgent",
  "status": "active",          // active | paused | circuit_broken
  "lastHeartbeat": "2026-05-22T08:30:00Z",
  "budgetUsed": 45.20,
  "budgetLimit": 100.00,
  "eventCount24h": 1247,
  "alertCount24h": 2,
  "anomalyScore": 0.12        // 0-1, higher = more anomalous
}
```

#### PUT `/api/guardrails/:agentId`
```json
{
  "maxSpendPerHour": 100,
  "maxSpendPerDay": 500,
  "maxTxPerMinute": 10,
  "allowedTools": ["swap", "bridge"],
  "blockedAddresses": ["0x..."],
  "autoCircuitBreak": true,
  "criticalThreshold": 3
}
```

---

## 11. Success Metrics

### Launch Phase (Month 1-2)
| Metric | Target |
|--------|--------|
| $VLTS Holders | 500+ unique holders |
| Farcaster followers | 2,000+ |
| Landing page visits | 5,000+ |
| Discord/Telegram members | 1,000+ |
| Token market cap | Track only (organic growth) |

### MVP Phase (Month 2-4)
| Metric | Target |
|--------|--------|
| Registered Agents | 50+ agents monitored |
| Daily Events Logged | 10,000+ events/day |
| Active Dashboard Users | 100+ weekly |
| Integrations | 3+ agent frameworks |
| Alerts Sent | Track accuracy & response rate |

### Growth Phase (Month 4-8)
| Metric | Target |
|--------|--------|
| Registered Agents | 500+ |
| Protocol Revenue | Track $VLTS fee volume |
| Partnerships | 5+ official integrations |
| Anomalies Detected | Track true positive rate |
| Compliance Reports Generated | Track enterprise adoption |

---

## 12. Risk Register

| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|
| Low initial adoption | Medium | High | Launch with free tier; integrate with popular frameworks first |
| Smart contract vulnerability | Low | Critical | Use OpenZeppelin; get community audit; start with simple contracts |
| Bankr security incident (reference) | N/A | N/A | We use Clanker, not Bankr; no custody of user funds |
| Token price volatility | High | Medium | Focus on utility & product; not on price |
| Competitor enters space | Medium | Medium | First-mover advantage; deep Base integration |
| Supabase free tier limits | Medium | Low | Migrate to paid tier once revenue flows |

---

## 13. Decisions (Resolved) ✅

| Question | Answer |
|----------|--------|
| Token Name | **VAELTUS / $VLTS** |
| Farcaster | ✅ Sudah punya |
| Domain | Disiapkan oleh founder (vaeltus.xxx) |
| Free Tier | **100% gratis** di MVP phase |
| Branding | Minimalist & elegant |
