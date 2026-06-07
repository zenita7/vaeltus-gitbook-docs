# Quickstart

This guide gets the VAELTUS monorepo running locally for development. It assumes
you want to run the dashboard and API first, then optionally run SDK tests,
contract tests, Supabase, and the agent simulator.

## Requirements

| Tool | Required For |
| --- | --- |
| Node.js 20 | Web app, TypeScript SDK, agent simulator, root workspace scripts |
| npm | Workspace installation and scripts |
| Python 3.9+ | Python SDK development and tests |
| Foundry | Solidity contract tests and deployment |
| Supabase CLI | Local database reset and local API integration testing |
| Base Sepolia wallet | Onchain simulator and testnet contract interactions |

`.nvmrc` is set to Node.js 20.

## Install

```bash
git clone --recurse-submodules https://github.com/zenita7/vaeltus.git
cd vaeltus
npm install
cp .env.example .env.local
```

If you cloned without submodules:

```bash
git submodule update --init --recursive
```

Fill `.env.local` with local Supabase, wallet, relayer, and contract values as
needed. Keep real credentials out of Git.

## Run The Web App

```bash
npm run dev
```

Open:

```text
http://localhost:3000
```

The root `dev` script delegates to the Next.js app in `apps/web`.

## Build And Verify

```bash
npm run lint
npm run test:sdk
npm run build
```

The root `verify` script runs the standard web lint, TypeScript SDK tests, and
build checks:

```bash
npm run verify
```

## Optional Local Services

Run Supabase locally:

```bash
supabase start
supabase db reset
```

Run contract tests:

```bash
cd contracts
forge test -vvv
```

Run Python SDK checks:

```bash
cd packages/python-sdk
python -m pip install -e ".[dev]"
python -m pytest -q
python -m mypy vaeltus
python -m ruff check vaeltus tests
```

## Run The Agent Simulator

Build the TypeScript SDK first:

```bash
npm run build --workspace=packages/sdk
```

Then start the simulator:

```bash
npm run start --workspace=apps/agent
```

The simulator needs a funded Base Sepolia wallet, OpenRouter key, deployed
contract addresses, and a running VAELTUS API.

## Common Scripts

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start the web app in development mode. |
| `npm run start:web` | Start the built web app. |
| `npm run lint` | Run web linting. |
| `npm run test` | Run TypeScript SDK and API tests. |
| `npm run test:sdk` | Run TypeScript SDK tests. |
| `npm run test:api` | Run web API integration tests when test Supabase env is configured. |
| `npm run test:e2e` | Run Playwright end-to-end tests for the web app. |
| `npm run test:contracts` | Run Foundry tests. |
| `npm run test:python` | Run Python SDK tests. |
| `npm run build` | Build web app and TypeScript SDK. |
