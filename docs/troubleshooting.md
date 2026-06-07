# Troubleshooting

Use this page for common local development and integration issues.

## Web App Does Not Start

Check dependencies:

```bash
npm install
```

Check env:

```bash
cp .env.example .env.local
```

Then start:

```bash
npm run dev
```

If the app needs Supabase-backed API routes, make sure Supabase env values are
set in `.env.local`.

## API Tests Skip

`npm run test:api` skips when test Supabase values are missing.

Set:

```bash
TEST_SUPABASE_URL=
TEST_SUPABASE_SERVICE_ROLE_KEY=
```

Use a disposable local or test project.

## Agent Cannot Connect

Check:

1. `VAELTUS_BASE_URL` points to a running API.
2. `VAELTUS_AGENT_ID` matches the agent UUID.
3. `VAELTUS_API_KEY` is the current non-revoked key.
4. The agent is active and not circuit-broken.
5. `baseUrl` uses HTTPS unless it is localhost, `127.0.0.1`, or `::1`.

The TypeScript SDK verifies credentials in `connect()` by calling
`GET /api/agents/:id`.

## Events Are Rejected

Common causes:

| Symptom | Cause |
| --- | --- |
| `401 Unauthorized` | API key is missing, revoked, wrong, or for a different agent. |
| `403 Agent is paused` | Agent is inactive from dashboard state. |
| `403 Agent is circuit-broken` | Circuit breaker must be reset before events are accepted. |
| `429` | Per-agent event rate limit was exceeded. |
| `400 eventType is required` | Event payload is missing `eventType`. |
| `400 severity must be info, warning, or critical` | Invalid severity value. |

## Heartbeats Are Rejected

Heartbeats require a valid agent API key and an active, non-circuit-broken
agent. The route rejects paused or circuit-broken agents with `403`.

## Guardrail Updates Fail

Check:

1. The browser has an active wallet session.
2. The session wallet owns the agent.
3. `blockedAddresses` contains only valid EVM addresses.
4. Supabase service role env is configured.
5. If `REQUIRE_ONCHAIN_SYNC=true`, relayer keys, chain RPC, contract addresses,
   and roles must be valid.

## Onchain Sync Fails

Check:

| Area | Verify |
| --- | --- |
| RPC | `BASE_SEPOLIA_RPC_URL` or `BASE_RPC_URL` is reachable. |
| Contracts | `NEXT_PUBLIC_REGISTRY_ADDRESS`, `NEXT_PUBLIC_BUDGET_GOVERNOR_ADDRESS`, and `NEXT_PUBLIC_EVENT_ANCHOR_ADDRESS` match the selected chain. |
| Relayer | `VAELTUS_RELAYER_PRIVATE_KEY` is funded and scoped. |
| Roles | Relayer has registrar, budget manager, watcher, or anchor roles as required. |
| Mode | Set `REQUIRE_ONCHAIN_SYNC=false` for best-effort local development. |

## Foundry Tests Fail Because Submodule Is Missing

Initialize submodules:

```bash
git submodule update --init --recursive
```

Then run:

```bash
cd contracts
forge test -vvv
```

## Python SDK Tests Fail

Install dev dependencies:

```bash
cd packages/python-sdk
python -m pip install -e ".[dev]"
python -m pytest -q
```

## GitBook Does Not Use The Docs Folder

Confirm the repository root has:

```yaml
root: ./docs/
structure:
  readme: README.md
  summary: SUMMARY.md
```

Confirm `docs/SUMMARY.md` links every page that should appear in the GitBook
sidebar.
