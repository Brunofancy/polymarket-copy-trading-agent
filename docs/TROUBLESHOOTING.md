# Troubleshooting

Common issues and fixes for `polymarket-copy-trading-bot-agent`.

## App fails to start: "No copy targets"

Cause:

- `trade.toml` has no valid `target_address` or `target_addresses`.

Fix:

- set one address string or a list under `[copy]`
- ensure values are proper strings, not nested objects

---

## UI loads blank or static assets fail

Cause:

- frontend not built, wrong `--ui-dir`, or missing `frontend/dist`.

Fix:

```bash
cd frontend && trunk build --release && cd ..
cargo run --release --bin main_copytrading
```

If using custom output dir, pass `--ui-dir` explicitly.

---

## `/api/state` works but no trades appear

Possible causes:

- simulation mode active and expected live orders
- leader has no recent position deltas
- filters are too strict (`entry_trade_sec`, `trade_sec_from_resolve`)
- `revert_trade = false` while expecting SELL copies

Fix:

- verify mode in logs/status
- temporarily relax filters
- confirm target addresses are active traders

---

## Agent tab/provider unavailable

Cause:

- no provider keys in `.env`.

Fix:

- add at least one of:
  - `OPENROUTER_API_KEY`
  - `OPENAI_API_KEY`
  - `ANTHROPIC_API_KEY`
- restart app

---

## Agent chat returns rate-limit/quota errors

Cause:

- provider-side quota/rate limits.

Fix:

- wait and retry
- switch provider in UI
- verify account billing/usage for selected provider

---

## Authentication or order placement errors

Cause:

- invalid CLOB credentials, wrong signature mode, bad private key, or mismatched wallet configuration.

Fix:

- re-check `config.json` values (`api_key`, `api_secret`, `api_passphrase`, `private_key`, `signature_type`)
- confirm wallet mode matches credential setup
- test with utility binaries (`test_allowance`, `test_cash_balance`, etc.)

---

## Exit rules not triggering

Cause:

- all exit values are `0` or no entries were recorded for tracked assets.

Fix:

- set non-zero values in `[exit]`
- verify buys are being tracked and positions are visible in state
- ensure bot is running long enough for periodic exit checks

---

## Port already in use

Cause:

- another process is bound to the configured `port`.

Fix:

- change `port` in `trade.toml`
- or stop conflicting process and restart

---

## TOML or JSON parse errors

Cause:

- malformed syntax in `trade.toml` or `config.json`.

Fix:

- validate commas, quotes, and section headers
- compare with `config.example.json` and repository `trade.toml`

---

## Slow or delayed updates in UI

Possible causes:

- aggressive poll interval settings
- API latency
- heavy local system load

Fix:

- tune `[copy].poll_interval_sec` appropriately
- watch logs for network/API warnings
- reduce concurrent workload on host machine
