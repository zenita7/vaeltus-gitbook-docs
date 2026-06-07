# Operations And Release

This page consolidates the release flow for the web app, SDKs, contracts, and
database.

## Pre-Release Checks

Run the checks that match the release surface:

```bash
npm run lint
npm run test:sdk
npm run build
```

Optional checks when tooling and env are available:

```bash
npm run test:e2e
npm run test:contracts
npm run test:python
```

Before any release:

1. Confirm `.env.local` is ignored and not staged.
2. Confirm all production secrets are configured outside Git.
3. Rotate any credential that ever reached a remote.
4. Review `.env.example` when env requirements change.
5. Review `.gitbook.yaml`, `docs/README.md`, and `docs/SUMMARY.md` when docs
   structure changes.

## Web App

Production build:

```bash
npm run build:web
```

Start locally from the production build:

```bash
npm run start:web
```

Production deployments must set env variables in the hosting provider, not in
the repository.

## TypeScript SDK

Package: `@vaeltus/sdk`.

```bash
npm run build:sdk
npm run test:sdk
npm publish --workspace=packages/sdk --access public --dry-run
```

Real publish:

```bash
npm publish --workspace=packages/sdk --access public
```

Tag-based workflow:

```bash
git tag sdk-v0.1.0
git push origin sdk-v0.1.0
```

## Python SDK

Package: `vaeltus-sdk`.

```bash
cd packages/python-sdk
python -m pip install -e ".[dev]"
python -m pytest -q
python -m mypy vaeltus
python -m ruff check vaeltus tests
python -m build
```

Tag-based workflow:

```bash
git tag python-sdk-v0.1.0
git push origin python-sdk-v0.1.0
```

## Contracts

```bash
cd contracts
forge test -vvv
forge script script/Deploy.s.sol \
  --rpc-url "$BASE_SEPOLIA_RPC_URL" \
  --broadcast \
  --verify \
  --etherscan-api-key "$BASESCAN_API_KEY"
```

Use a dedicated deployer wallet. Move ownership and roles deliberately after
deployment.

## Supabase

Local reset:

```bash
supabase start
supabase db reset
```

Hosted projects should receive migrations through the team's standard Supabase
deployment flow. Keep seed data separate from production data.

## GitBook

GitBook sync should use:

```text
.gitbook.yaml
docs/README.md
docs/SUMMARY.md
```

When adding pages:

1. Add the Markdown file under `docs/`.
2. Link it from `docs/SUMMARY.md`.
3. Verify every relative link points to an existing file.
4. Keep source-of-truth code references as paths, not copied implementation.

## Release Notes

For each release, capture:

| Field | Notes |
| --- | --- |
| Version or tag | Package tag, deployment version, or commit SHA. |
| Surface | Web, TypeScript SDK, Python SDK, contracts, database, docs. |
| Changes | User-facing behavior changes and breaking changes. |
| Migration notes | Required env, database, contract, or role changes. |
| Verification | Commands and environments used. |
| Rollback | How to revert or disable the release surface. |
