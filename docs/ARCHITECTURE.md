# Architecture

This project is a Rust workspace-style application with:

- a backend that streams, copies, and manages trades
- an API/web server for state + agent endpoints
- a Rust/WASM frontend (built with Trunk)

## High-Level Runtime Flow

1. Load credentials from `config.json`.
2. Load copy rules from `trade.toml`.
3. Start web server and dashboard endpoints.
4. Start activity stream and polling loops.
5. Convert leader position deltas into candidate trades.
6. Apply filters and risk constraints.
7. Execute or simulate orders.
8. Update shared web state and push notifications.

## Key Directories

- `src/` - core backend modules
- `src/bin/` - runnable bot entry points
- `frontend/` - web UI source (WASM target)
- `scripts/` - helper scripts for serving/screenshot tasks

## Main Copy-Trading Entry Point

- `src/bin/main_copytrading.rs`

Responsibilities:

- parse CLI args (`config`, `trade_config`, `simulation`, `ui_dir`)
- initialize API clients and authentication
- load `CopyTradingConfig`
- start web server (`axum`)
- set up SSE and state endpoints
- spawn copy stream + optional exit loop
- poll and publish portfolio positions

## Core Modules

- `src/api.rs`  
  Polymarket API client wrapper for positions, auth, order placement.

- `src/clob_sdk.rs`  
  CLOB SDK integration, chain utilities (`polygon()` etc.).

- `src/copy_trading.rs`  
  Copy config structs, filter rules, delta-to-trade conversion, order copy logic, exit loop.

- `src/activity_stream.rs`  
  Real-time ingestion of activity/events that trigger copy logic.

- `src/web_state.rs`  
  Shared state (`RwLock`) for logs, status, positions, UI settings.

- `src/logging.rs`  
  History/event logging helpers.

## Shared State Model

`BotState` includes:

- `logs` (recent trade/system events)
- `status` (mode, target count, wallet, targets list)
- `positions` (per-user position summaries and deltas)
- `ui` (highlight/animation timing)

This state is exposed as JSON via `/api/state`.

## HTTP API Surface (`main_copytrading`)

- `GET /api/state`  
  Current bot/dashboard state.

- `GET /api/state/stream`  
  SSE update signal stream (`update` events).

- `GET /api/leaderboard`  
  Proxy query to Polymarket data leaderboard.

- `GET /api/agent/providers`  
  Available AI providers based on keys in `.env`.

- `POST /api/agent/chat`  
  AI chat endpoint (OpenRouter/OpenAI/Anthropic).

SPA routes (served `index.html`): `/`, `/logs`, `/settings`, `/toptraders`, `/agent`, `/portfolio`.

## Copy-Trade Logic Model

Trade generation:

- compare current vs previous position snapshots
- infer BUY/SELL deltas per `asset_id`
- generate internal `LeaderTrade` records

Filtering:

- optional stale-trade cutoff (`entry_trade_sec`)
- optional near-resolution cutoff (`trade_sec_from_resolve`)
- optional SELL mirroring control (`revert_trade`)

Execution:

- BUY size = leader size * `size_multiplier`
- optional BUY notional cap (`buy_amount_limit_in_usd`)
- order placement through market order call (`FOK`)

Risk management:

- optional exit loop checks open positions periodically
- triggers on take-profit, stop-loss, or trailing-stop percent thresholds

## Binaries Overview

Primary:

- `main_copytrading` - copy trading + dashboard + agent endpoints.

Other strategy binaries (from `Cargo.toml`):

- `main_sports_trailing`
- `main_trailing`
- `main_dual_limit_045_same_size`
- `main_dual_limit_045_5m_btc`
- `backtest`

Utility/test binaries:

- `test_allowance`
- `test_cash_balance`
- `test_limit_order`
- `test_merge`
- `test_multiple_orders`
- `test_predict_fun`
- `test_redeem`
- `test_sell`

## Frontend Notes

- Built from `frontend/` with Trunk
- output is served by backend from `frontend/dist` by default
- frontend consumes `/api/state` and SSE stream to keep UI live

## Concurrency Model

The backend uses `tokio` tasks for:

- web server
- stream handling
- polling loops
- background auth attempts
- exit loop checks
- async provider requests for AI chat

Shared mutable structures are protected via `Arc<Mutex<...>>` or `Arc<RwLock<...>>`.
