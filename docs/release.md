# Release Guide

## Pre-Release Checks

Run the checks that match the release surface:

```bash
npm run lint
npm run test:sdk
npm run build
```

Optional checks when the tooling is available:

```bash
npm run test:e2e
npm run test:contracts
npm run test:python
```

Before publishing or deploying:

- Confirm `.env.local` is ignored and not staged.
- Confirm all production secrets are configured outside Git.
- Rotate any credential that ever reached a remote.
- Review `.env.example` when env requirements change.

## TypeScript SDK

The npm package is `@vaeltus/sdk`.

```bash
npm run build:sdk
npm run test:sdk
npm publish --workspace=packages/sdk --access public --dry-run
```

Real publish:

```bash
npm publish --workspace=packages/sdk --access public
```

Tag-based GitHub publish workflow:

```bash
git tag sdk-v0.1.0
git push origin sdk-v0.1.0
```

## Python SDK

The Python package is `vaeltus-sdk`.

```bash
cd packages/python-sdk
python -m pip install -e ".[dev]"
python -m pytest -q
python -m build
```

Tag-based GitHub publish workflow:

```bash
git tag python-sdk-v0.1.0
git push origin python-sdk-v0.1.0
```

## Web App

Production build:

```bash
npm run build:web
```

Start locally from the production build:

```bash
npm run start:web
```

For hosted deployment, set env variables in the hosting provider, not in the repo.

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

Use a dedicated deployer wallet and move ownership/roles deliberately after deployment.
