# VAELTUS Documentation

VAELTUS is an onchain AI agent observability and guardrails protocol for Base.
It helps operators register autonomous agents, stream runtime events, monitor
heartbeats, enforce budgets and permissions, and anchor critical activity to
onchain contracts.

This GitBook is the operational and developer reference for the VAELTUS
monorepo. It covers the web dashboard, API routes, TypeScript SDK, Python SDK,
agent simulator, Supabase schema, smart contracts, and release workflow.

## What VAELTUS Provides

| Surface | Purpose |
| --- | --- |
| Web dashboard | Wallet-authenticated operator console for agent status, alerts, analytics, guardrails, and settings. |
| API routes | Next.js API layer for registration, events, heartbeats, guardrails, alerts, auth, cron, and notification settings. |
| TypeScript SDK | `@vaeltus/sdk` client for agent event logging, heartbeat reporting, tracing, and local guardrail checks. |
| Python SDK | `vaeltus-sdk` async client with equivalent event, heartbeat, tracing, and guardrail helpers. |
| Agent simulator | Base Sepolia autonomous agent runner that exercises the protocol end to end. |
| Smart contracts | Registry, budget governor, event anchor, and circuit breaker contracts for Base. |
| Supabase | Persistence layer for agents, guardrails, events, alerts, heartbeats, audit logs, rate limits, and notification settings. |

## Recommended Reading Path

1. Start with [Quickstart](getting-started.md) to run the project locally.
2. Read [Architecture](architecture.md) and [Core Workflows](workflows.md) to understand how data moves through the system.
3. Use [API Reference](api-reference.md), [TypeScript SDK](typescript-sdk.md), or [Python SDK](python-sdk.md) when integrating an agent.
4. Review [Database and RLS](database.md), [Smart Contracts](smart-contracts.md), and [Security Model](security.md) before deploying.
5. Follow [Operations and Release](operations.md) for release and production checks.

## Repository Map

```text
apps/web              Next.js app, dashboard, API routes, styles, and tests
apps/agent            Base Sepolia agent simulator and onchain demo runner
packages/sdk          TypeScript SDK published as @vaeltus/sdk
packages/python-sdk   Python SDK published as vaeltus-sdk
contracts             Foundry contracts, tests, deployment script, and submodule
supabase              Database migrations, RLS policies, seed data, and notes
docs                  GitBook source and project documentation
```

`contracts/lib/forge-std` is tracked as a git submodule. Clone with
`--recurse-submodules` or run `git submodule update --init --recursive`.

## Source Of Truth

| Area | Source |
| --- | --- |
| Environment template | [../.env.example](../.env.example) |
| Web app | [../apps/web](../apps/web) |
| Agent simulator | [../apps/agent](../apps/agent) |
| TypeScript SDK | [../packages/sdk](../packages/sdk) |
| Python SDK | [../packages/python-sdk](../packages/python-sdk) |
| Contracts | [../contracts](../contracts) |
| Supabase migrations | [../supabase](../supabase) |

## GitBook Sync

The repository root contains `.gitbook.yaml`, which points GitBook to this
`docs/` directory. GitBook should use this file as the documentation root:

```yaml
root: ./docs/
structure:
  readme: README.md
  summary: SUMMARY.md
```
