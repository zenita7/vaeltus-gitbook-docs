# Python SDK

The Python SDK package is `vaeltus-sdk` and lives in `packages/python-sdk`.

It provides an async client for agent registration, event logging, heartbeats,
tracing, and local guardrail checks.

## Install

```bash
pip install vaeltus-sdk
```

Requires Python 3.9+.

## Quick Start

```python
import asyncio
from vaeltus import Vaeltus
from vaeltus.types import GuardrailConfig, VaeltusConfig

config = VaeltusConfig(
    agent_id="your-agent-id",
    api_key="vlts_your_api_key",
    base_url="https://vaeltus.xyz",
    guardrails=GuardrailConfig(
        max_spend_per_hour_usd=50.0,
        max_spend_per_day_usd=250.0,
        allowed_tools=["web_search", "code_exec"],
        blocked_addresses=["0xbadactor"],
        max_tx_per_minute=5,
    ),
)

async def main():
    async with Vaeltus.connect(config) as client:
        await client.log_tool_call(
            "web_search",
            {"query": "ETH price"},
            {"price": "$3,200"},
            success=True,
            latency_ms=320,
        )

        await client.log_transaction(
            tx_hash="0xabc...",
            chain="base",
            from_address="0xYourWallet",
            to="0xProtocol",
            value_usd=10.0,
            method="deposit",
        )

        await client.log_error("Slippage exceeded", {"expected": 0.5, "actual": 2.3})

asyncio.run(main())
```

## Configuration

| Field | Type | Default |
| --- | --- | --- |
| `agent_id` | `str` | Required |
| `api_key` | `str` | Required |
| `base_url` | `str` | `https://vaeltus.xyz` |
| `guardrails` | `GuardrailConfig \| None` | `None` |
| `heartbeat_interval` | `float` | `30.0` seconds |
| `debug` | `bool` | `False` |
| `fail_mode` | `"open" \| "closed"` | `"open"` |
| `max_retries` | `int` | `3` |
| `retry_delay` | `float` | `0.5` seconds |

## Guardrail Config

| Field | Type | Default |
| --- | --- | --- |
| `max_spend_per_hour_usd` | `float` | `0.0` |
| `max_spend_per_day_usd` | `float` | `0.0` |
| `allowed_tools` | `list[str]` | `[]` |
| `blocked_addresses` | `list[str]` | `[]` |
| `max_tx_per_minute` | `int` | `0` |

## Register A New Agent

```python
from vaeltus import Vaeltus
from vaeltus.types import RegisterPayload

result = await Vaeltus.register(
    RegisterPayload(
        name="MyDeFiBot",
        agent_type="defi",
        description="Automated yield optimizer",
        owner_address="0xYourWallet",
    )
)

print(result.agent_id, result.api_key, result.onchain_id)
```

## Guardrail Checks

```python
budget_ok = client.check_budget(10.0)
tool_ok = client.check_tool_allowed("uniswap_swap")
addr_ok = client.check_address("0xsomeaddress")

if not budget_ok["allowed"]:
    print(budget_ok["reason"])
```

## Development

```bash
cd packages/python-sdk
python -m pip install -e ".[dev]"
python -m pytest -q
python -m mypy vaeltus
python -m ruff check vaeltus tests
python -m build
```

Tag-based publish automation uses tags like:

```bash
git tag python-sdk-v0.1.0
git push origin python-sdk-v0.1.0
```
