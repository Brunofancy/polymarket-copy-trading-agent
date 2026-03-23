# Setup Guide

This guide gets you from a fresh machine to a running dashboard and copy-trading backend.

## 1) Prerequisites

- Rust toolchain (`1.70+` recommended)
- `cargo`
- `rustup`
- Polymarket account and credentials
- USDC on Polygon for live order execution

Frontend build dependencies:

```bash
cargo install trunk
rustup target add wasm32-unknown-unknown
```

## 2) Clone Repository

```bash
git clone https://github.com/brunobmtx/polymarket-copy-trading-bot-agent.git
cd polymarket-copy-trading-bot-agent
```

## 3) Create `config.json`

Use `config.example.json` as your base.

```json
{
  "polymarket": {
    "gamma_api_url": "https://gamma-api.polymarket.com",
    "clob_api_url": "https://clob.polymarket.com",
    "api_key": "YOUR_API_KEY",
    "api_secret": "YOUR_API_SECRET",
    "api_passphrase": "YOUR_API_PASSPHRASE",
    "private_key": "YOUR_PRIVATE_KEY",
    "proxy_wallet_address": "",
    "signature_type": 2
  },
  "trading": {
    "slug": "optional-sports-market-slug",
    "continuous": false,
    "check_interval_ms": 1000,
    "trailing_stop_point": 0.03,
    "trailing_shares": 10.0,
    "fixed_trade_amount": 1.0,
    "min_time_remaining_seconds": 30,
    "sell_price": 0.99
  }
}
```

Notes:

- `api_key/api_secret/api_passphrase` are CLOB credentials.
- `private_key` signs transactions.
- `signature_type` varies by wallet mode (`EOA`, `Proxy`, `GnosisSafe` style usage in project docs/comments).

## 4) Create `trade.toml`

Use the repository `trade.toml` as a template and tune it for your risk profile.

Minimal copy-trading example:

```toml
clob_host = "https://clob.polymarket.com"
chain_id = 137
port = 8000
simulation = true

[copy]
target_address = "0xYourLeaderAddress"
revert_trade = false
size_multiplier = 0.05
poll_interval_sec = 0.5

[exit]
take_profit = 5
stop_loss = 5
trailing_stop = 3

[filter]
buy_amount_limit_in_usd = 10
entry_trade_sec = 30
trade_sec_from_resolve = 300

[ui]
delta_highlight_sec = 10
delta_animation_sec = 2
```

## 5) Optional: Configure AI Agent

Create `.env` in the project root:

```env
OPENROUTER_API_KEY=sk-or-...
# OPENAI_API_KEY=...
# ANTHROPIC_API_KEY=...
```

Optional model controls:

```env
OPENROUTER_MODEL=anthropic/claude-3.5-sonnet
OPENAI_MODEL=gpt-4o-mini
ANTHROPIC_MODEL=claude-3-5-sonnet-20241022
OPENROUTER_MAX_TOKENS=512
```

## 6) Build Frontend

```bash
cd frontend
trunk build --release
cd ..
```

## 7) Run Copy-Trading App

```bash
cargo run --release --bin main_copytrading
```

Open:

- `http://localhost:8000`

The app also binds to `0.0.0.0`, so LAN access is possible at `http://<host-ip>:8000`.

## 8) Run in Simulation Mode (Recommended First)

```bash
cargo run --release --bin main_copytrading -- --simulation
```

Simulation logs and UI updates still run, but orders are not executed on-chain.

## 9) Verify Basic Health

After startup, verify:

- UI loads
- `/api/state` returns JSON
- target addresses appear in status
- logs stream updates as data arrives
- if AI key exists, agent providers are visible in the UI

## 10) Security Checklist

- never commit `config.json` or `.env`
- use a dedicated low-balance wallet for testing
- start with `simulation = true`
- cap `buy_amount_limit_in_usd`
- set conservative `size_multiplier`
- enable at least one exit control before live usage
