# Runbook

Operational guide for running bots safely in development and production-like environments.

## Build Steps

Backend:

```bash
cargo build --release
```

Frontend:

```bash
cd frontend
trunk build --release
cd ..
```

## Core Runtime Commands

### Copy trading app

```bash
cargo run --release --bin main_copytrading
```

With explicit config paths:

```bash
cargo run --release --bin main_copytrading -- --config config.json --trade-config trade.toml
```

Simulation mode:

```bash
cargo run --release --bin main_copytrading -- --simulation
```

Custom UI dir:

```bash
cargo run --release --bin main_copytrading -- --ui-dir frontend/dist
```

### Other binaries

```bash
cargo run --release --bin main_sports_trailing
cargo run --release --bin main_trailing
cargo run --release --bin main_dual_limit_045_same_size
cargo run --release --bin main_dual_limit_045_5m_btc
cargo run --release --bin backtest
```

## Startup Checklist

Before starting:

- `config.json` contains valid Polymarket keys
- `trade.toml` contains at least one target address
- frontend has been built (`frontend/dist` exists)
- `.env` exists only if agent providers are needed

After starting:

- UI reachable at `http://localhost:<port>`
- `/api/state` responds
- logs show mode (`SIMULATION` or `LIVE`)
- target address count is correct

## Live Trading Safety Checklist

Minimum recommended controls before live mode:

- set `size_multiplier` to a small value
- set `buy_amount_limit_in_usd` > 0
- define at least one exit rule (`take_profit`, `stop_loss`, or `trailing_stop`)
- run simulation first and inspect logs for a full cycle
- verify wallet balance and allowance behavior with utility binaries

## Deployment Notes

- The app binds to `0.0.0.0:<port>`.
- Access from another machine using `http://<server-ip>:<port>`.
- Keep secrets out of source control.
- Use process supervision (`systemd`, container runtime, or tmux/screen) for long-running sessions.

## Suggested Environment Layout

- `config.json` and `trade.toml` in project root
- `.env` in project root
- separate wallet/key set for staging/simulation and production

## Logging and State

- Runtime state is exposed through `/api/state`.
- SSE notifications are emitted through `/api/state/stream`.
- Recent UI logs are in-memory (trimmed to a bounded list).

## Operational Playbooks

### Rotate leader addresses

1. Update `[copy]` target addresses in `trade.toml`.
2. Restart `main_copytrading`.
3. Confirm new targets appear in UI status.

### Emergency stop

1. Stop the running process.
2. Switch `simulation = true` in `trade.toml` (or add `--simulation`).
3. Restart and verify no live order placement.

### Provider failover for AI agent

1. Add additional provider keys in `.env`.
2. Restart app.
3. Select alternate provider from Agent UI if one provider is rate-limited.
