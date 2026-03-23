# Polymarket Copy Trading Bot - Documentation

This directory contains project documentation for setup, architecture, configuration, operations, and troubleshooting.

## Documentation Map

- `docs/SETUP.md`  
  End-to-end local setup: prerequisites, config files, build, run, simulation mode.

- `docs/CONFIGURATION.md`  
  Full reference for `config.json`, `trade.toml`, and `.env` variables used by the bot and agent UI.

- `docs/ARCHITECTURE.md`  
  Backend/frontend design, runtime flow, API endpoints, and module responsibilities.

- `docs/RUNBOOK.md`  
  Practical run commands for each binary, deployment basics, and safe operating checklist.

- `docs/TROUBLESHOOTING.md`  
  Common errors, root causes, and concrete fixes.

## What This Project Is

`polymarket-copy-trading-bot-agent` is a Rust-based Polymarket trading toolkit that combines:

- copy trading from one or more leader addresses
- strategy bots (trailing, dual-limit, sports market variants)
- simulation/backtest utilities
- real-time web dashboard (`axum` backend + Rust/WASM frontend)
- optional AI research assistant (OpenRouter/OpenAI/Anthropic)

The project is designed to keep execution and monitoring local, with explicit control over risk settings.

## Suggested Reading Order

1. `docs/SETUP.md`
2. `docs/CONFIGURATION.md`
3. `docs/RUNBOOK.md`
4. `docs/ARCHITECTURE.md`
5. `docs/TROUBLESHOOTING.md`
