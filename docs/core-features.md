# VAELTUS — Core Features

> Onchain AI Agent Observability & Guardrails Protocol on Base

---

## 1. Agent Registry

**What:** Register AI agent secara onchain dengan identitas unik (agentId), bound ke wallet owner.

**Solve:**
- **No Identity Layer** — Saat ini tidak ada standard registry untuk AI agents onchain. Setiap framework (ElizaOS, Virtuals) punya ID system sendiri. VAELTUS jadi canonical source of truth: siapa ownernya, apa typenya, kapan diregister.
- **Sybil & Impersonation** — Tanpa registry onchain, siapapun bisa claim agent yang bukan miliknya. AgentId di-hash dan di-anchor ke Base, memberikan cryptographic proof of ownership.
- **Discovery** — Developer dan user bisa browse agent yang terdaftar, lihat tipe dan deskripsinya, sebelum integrate atau invest.

**Flow:** Wallet connect → register form → sign tx → `VaeltusRegistry.registerAgent()` → dapat API key + onchainId.

---

## 2. Real-Time Event Logger

**What:** Setiap aksi agent (tool call, transaction, reasoning step, error) di-log via SDK dan dikirim ke API, disimpan di Supabase, dan di-streaming via Realtime ke dashboard.

**Solve:**
- **Black Box Decisions** — Agent builders tidak bisa trace "kenapa" agent membuat keputusan tertentu. Setiap tool call dan reasoning step dicatat dengan full payload JSON, timestamp, dan severity. Dashboard menampilkan log dalam terminal view yang bisa di-expand untuk inspect raw JSON.
- **Debugging Slow** — Tanpa log terpusat, debugging agent harus manual SSH atau grep container log. Dengan VAELTUS, semua event realtime muncul di dashboard tanpa refresh, dengan filter severity, event type, dan text search.
- **No Audit Trail** — Compliance (EU AI Act 2026) mewajibkan audit trail untuk autonomous systems. VAELTUS memberikan immutable log yang bisa di-anchor ke Base dengan transaction hash.

**Tech:** Supabase Realtime subscription → `dashboard-store.ts` → Zustand state → UI auto-update.

---

## 3. Budget Governor

**What:** Set spend limit per agent — per jam, per hari, dan per transaksi. SDK melakukan client-side check sebelum transaksi; API melakukan server-side enforcement.

**Solve:**
- **Cost Spirals** — Agent bisa stuck di retry loop yang menghabiskan gas fee tanpa batas. Budget governor memotong transaksi saat limit tercapai, baik dari SDK (client-side pre-check) maupun API (server-side enforcement).
- **No Spending Visibility** — Operator tidak tahu berapa yang sudah di-spend agentnya. BudgetMeter di dashboard menampilkan progress bar realtime: spent vs limit, dengan threshold warning di 80%.
- **Single Point of Control** — Daripada set limit di tiap tool/config, VAELTUS jadi single pane untuk budget policy semua agent.

**Config:** `maxSpendPerHourUsd`, `maxSpendPerDayUsd`, `maxTxPerMinute`.

---

## 4. Circuit Breaker

**What:** Emergency halt — manual (operator trip via dashboard) atau otomatis (heartbeat miss > 10 menit atau guardrail violation threshold). Semua di-anchor ke Base via `CircuitBreaker.sol`.

**Solve:**
- **Silent Failures** — Agent bisa stuck, loop, atau mulai berperilaku aneh tanpa diketahui operator. Heartbeat monitor cron mendeteksi agent yang tidak melapor dalam 2 menit → auto-alert; dalam 10 menit → auto circuit-break.
- **No Kill Switch** — Jika agent diretas atau bug, tidak ada cara instan untuk menghentikannya. Circuit breaker memberikan operator satu tombol untuk langsung menghentikan agent, dengan reasoning yang dicatat di audit log.
- **Regulatory Compliance** — EU AI Act mensyaratkan "human-in-the-loop" override untuk autonomous systems. Circuit breaker adalah implementasi konkret dari requirement ini.

**Flow:** Operator trip → `circuitBreakOnchain()` → API validation rejected → agent stopped. Reset hanya bisa oleh owner.

---

## 5. Alert System

**What:** Multi-channel notification (webhook, Telegram, Farcaster — plan) saat terjadi anomaly, budget exceeded, heartbeat miss, atau circuit break.

**Solve:**
- **Reactive Monitoring** — Operator tidak mungkin menatap dashboard 24/7. Alerts mendorong notifikasi ke channel yang sudah mereka pakai (Telegram bot, webhook ke Discord/Slack).
- **Alert Fatigue Prevention** — Severity classification (info/warning/critical) dan per-agent notification settings memastikan operator hanya dapat alert yang relevan. Alerts yang sudah diakui tidak dikirim ulang.
- **Incident Response** — Timestamped alerts dengan severity + agent context memudahkan post-mortem analysis dan SLA tracking.

**Delivery Chain:** Event terjadi → guardrail check → alert created in DB → cron fetch undelivered → POST webhook/Telegram → stamp `notified_at`.

---

## 6. Guardrail Policies

**What:** Whitelist allowed tools, blacklist EVM addresses, set transaction rate limits, auto circuit-break threshold.

**Solve:**
- **Over-Permissioning** — Agent sering diberi akses terlalu luas ("call any contract", "use any tool"). Guardrail policies membatasi agent hanya ke tools dan addresses yang di-allow.
- **Policy Drift** — Config agent berubah seiring waktu tanpa dokumentasi. Semua perubahan guardrail di-sync ke onchain (via `BudgetGovernor.sol`) dan dicatat di audit log, memberikan tamper-proof history.
- **Reusable Templates** — (V2) Guardrail config bisa disimpan sebagai template dan diterapkan ke multiple agents, mengurangi duplication.

**Config per agent:** `allowedTools[]`, `blockedAddresses[]`, `maxTxPerMinute`, `autoCircuitBreak`, `criticalThreshold`.

---

## 7. Observability Dashboard

**What:** Web app dengan 6 halaman utama — Overview, Agent Detail, Alerts, Analytics, Guardrails, Settings.

**Solve:**
- **Single Pane of Glass** — Operator tidak perlu buka 5 tools berbeda untuk monitor agent. Dashboard menggabungkan realtime event log, spending charts, alert summary, dan guardrail editor dalam satu UI.
- **Data-Driven Decisions** — Analytics page menampilkan: spend velocity per agent, tool invocation distribution (pie chart), severity breakdown — membantu operator identifikasi pola dan optimasi.
- **Operator Workflow** — Dari overview → klik agent → lihat event/alert/guardrail → edit config → trip breaker. Semua dalam flow yang cohesive tanpa context switching.

**Pages:**
| Page | Purpose |
|---|---|
| `/dashboard` | Registry overview + status grid + register modal |
| `/dashboard/agents/[id]` | Single agent: events, guardrails, credential controls |
| `/dashboard/alerts` | Incident hub: severity filter, acknowledge, status |
| `/dashboard/analytics` | Charts: spending, tool distribution, severity pie |
| `/dashboard/guardrails` | Consolidated guardrail config for all agents |
| `/dashboard/settings` | Wallet info, API key management, audit log |

---

## 8. VAELTUS SDK

**What:** TypeScript library (`@vaeltus/sdk`) yang di-install di agent codebase untuk integrasi one-liner.

**Solve:**
- **Integration Friction** — Tanpa SDK, developer harus bikin manual: HTTP client, retry logic, guardrail checks. SDK memberikan `new Vaeltus(config)` → `await agent.connect()` → gunakan `logToolCall()`, `logTransaction()`, `trace()`.
- **Inconsistent Guardrails** — Tanpa SDK, tiap developer implementasi guardrail sendiri-sendiri (atau tidak sama sekali). SDK melakukan client-side pre-check: tool whitelist, spend limit, address blocklist, tx rate — semua sebelum request ke API.
- **Fail-Safe Design** — `failMode: 'open'` memastikan agent tetap jalan meskipun VAELTUS API down (graceful degradation). `failMode: 'closed'` untuk use case yang butuh strict enforcement.

**Key APIs:**
```typescript
Vaeltus.register(payload)        // static - one-time registration
agent.connect()                   // verify + start heartbeat
agent.logToolCall(tool, in, out)  // typed tool call logging
agent.logTransaction(tx, chain)   // onchain tx logging
agent.logError(msg, ctx)          // error with severity=critical
agent.trace('span', async fn)     // wrap async with duration + log
agent.checkBudget(amount)         // dry-run spend check
agent.checkToolAllowed(tool)      // tool whitelist check
agent.checkAddress(addr)          // blocklist check
```

---

## 9. Smart Contracts (Base)

**What:** 4 Solidity contracts di-deploy ke Base untuk onchain anchoring dan enforcement.

| Contract | Role |
|---|---|
| `VaeltusRegistry.sol` | Register agent — owner, name, type, agentId |
| `BudgetGovernor.sol` | Enforce spend limits — max per tx/hour/day |
| `EventAnchor.sol` | Anchor event hash ke onchain → immutable proof |
| `CircuitBreaker.sol` | Emergency halt agent — hanya owner yang bisa trip/reset |

**Solve:**
- **Trust Minimization** — Tidak perlu percaya VAELTUS server. Semua aksi kritikal (register, budget sync, circuit break, event anchor) disimpan di Base. Operator bisa verify independent.
- **Tamper-Proof Audit** — Event hashes di-anchor ke `EventAnchor.sol`, memberikan cryptographic proof bahwa event terjadi pada waktu tertentu. Tidak bisa dihapus atau dimodifikasi.
- **Decentralized Enforcement** — Guardrail policies di-enforce di smart contract level, bukan hanya di server. Bahkan jika API down, contract tetap bisa memvalidasi.

---

## 10. Wallet Authentication

**What:** Sign-in with Ethereum — nonce challenge → signed message → HMAC-signed session cookie.

**Solve:**
- **No Password/Email** — Web3-native auth. Tidak perlu registration form untuk dashboard; wallet address jadi identity.
- **Owner-Only Access** — Semua route mutasi (register, edit guardrails, circuit break, key rotation) di-enforce: hanya wallet yang punya agent yang bisa modifikasi.
- **No Session Hijack** — Session cookie di-HMAC dengan `VAELTUS_SESSION_SECRET`, expire otomatis, tidak bisa di-forge tanpa secret.

---

## Architecture at a Glance

```
┌───────────────┐    ┌────────────────┐    ┌─────────────────┐
│  Agent Code   │───▶│  VAELTUS SDK   │───▶│  Next.js API    │
│  (ElizaOS/    │    │  (TypeScript)   │    │  Routes         │
│   Custom)     │    │                 │    │                 │
└───────────────┘    │ • logToolCall  │    │ • /register     │
                     │ • logTx        │    │ • /events       │
                     │ • trace        │    │ • /heartbeat    │
                     │ • guardrails   │    │ • /guardrails   │
                     └────────────────┘    └───────┬─────────┘
                                                   │
                    ┌──────────────────────────────┼──────────────┐
                    │                              │              │
              ┌─────▼─────┐              ┌─────────▼──────┐  ┌───▼──────────┐
              │  Supabase  │              │  Base L2       │  │  Dashboard   │
              │  (Offchain)│              │  (Onchain)      │  │  (Next.js)   │
              │            │              │                 │  │              │
              │ • agents   │              │ • Registry      │  │ • Overview   │
              │ • events   │              │ • BudgetGov     │  │ • Analytics  │
              │ • alerts   │              │ • EventAnchor   │  │ • Guardrails │
              │ • guardrail│              │ • CircuitBreaker│  │ • Settings   │
              │ • Realtime │              │                 │  │ • Realtime   │
              └─────┬──────┘              └─────────────────┘  └──────────────┘
                    │
              ┌─────▼──────┐
              │  Alerts    │
              │  Delivery  │
              │            │
              │ • Webhook  │
              │ • Telegram │
              │ • Farcaster│
              └────────────┘
```
