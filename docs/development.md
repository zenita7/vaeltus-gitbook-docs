# Development Guide

## Repository Layout

```text
apps/web              Next.js app, dashboard, API routes, styles, and tests
packages/sdk          TypeScript SDK published as @vaeltus/sdk
packages/python-sdk   Python SDK published as vaeltus-sdk
contracts             Foundry contracts, tests, deployment script, and submodules
supabase              Database migrations, RLS policies, seed data, and notes
docs                  Product, SDK, development, and release docs
```

## Setup

```bash
npm install
cp .env.example .env.local
git submodule update --init --recursive
```

Use Node.js 20. `.nvmrc` is the source of truth.

## Environment

`.env.example` is committed as a blank template. `.env.local` is ignored and should contain real local values. Production secrets belong in the deployment platform, GitHub Actions secrets, Supabase dashboard, npm, PyPI, or the relevant provider.

## Root Commands

```bash
npm run dev
npm run start:web
npm run lint
npm run test:sdk
npm run test:api
npm run test:e2e
npm run test:contracts
npm run test:python
npm run build
```

## Web App

```bash
npm run dev:web
npm run build:web
npm run start:web
```

Use production mode for LAN mobile checks:

```bash
npm run build:web
npm run start:web -- -p 3000 -H 0.0.0.0
```

## TypeScript SDK

```bash
npm run build:sdk
npm run test:sdk
npm publish --workspace=packages/sdk --access public --dry-run
```

## Python SDK

```bash
cd packages/python-sdk
python -m pip install -e ".[dev]"
python -m pytest -q
python -m mypy vaeltus
python -m ruff check vaeltus tests
```

## Contracts

```bash
cd contracts
forge test -vvv
```

`contracts/lib/forge-std` is a git submodule. Clone with `--recurse-submodules` or run:

```bash
git submodule update --init --recursive
```

## Supabase

```bash
supabase start
supabase db reset
```

API integration tests skip automatically unless `TEST_SUPABASE_URL` and `TEST_SUPABASE_SERVICE_ROLE_KEY` are set.
