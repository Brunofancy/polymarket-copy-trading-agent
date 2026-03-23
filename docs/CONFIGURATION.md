# Configuration Reference

This file documents runtime configuration used by the project.

There are three main sources:

- `config.json` (Polymarket API + strategy config)
- `trade.toml` (copy trading + UI/runtime config)
- `.env` (optional AI provider keys and model overrides)

---

## `config.json`

Loaded by `Config::load()` from `src/config.rs`. If the file does not exist, defaults may be created automatically.

### `polymarket`

- `gamma_api_url` (`string`)  
  Gamma API base URL. Default: `https://gamma-api.polymarket.com`.

- `clob_api_url` (`string`)  
  CLOB API base URL. Default: `https://clob.polymarket.com`.

- `api_key` (`string | null`)  
  CLOB API key for authenticated operations.

- `api_secret` (`string | null`)  
  CLOB API secret.

- `api_passphrase` (`string | null`)  
  CLOB API passphrase.

- `private_key` (`string | null`)  
  Wallet private key for signing orders.

- `proxy_wallet_address` (`string | null`)  
  Optional proxy wallet address.

- `signature_type` (`number | null`)  
  Wallet signing mode identifier.

### `trading`

Used mainly by non-copy strategy binaries (`main_trailing`, `main_sports_trailing`, dual-limit variants).

Important keys include:

- market selectors: `slug`, `eth_condition_id`, `btc_condition_id`, `solana_condition_id`, `xrp_condition_id`
- timing controls: `check_interval_ms`, `min_elapsed_minutes`, `market_closure_check_interval_seconds`
- sizing and triggers: `fixed_trade_amount`, `trigger_price`, `sell_price`, `max_buy_price`
- risk controls: `stop_loss_price`, `hedge_price`, `min_time_remaining_seconds`
- feature toggles: `enable_btc_trading`, `enable_eth_trading`, `enable_solana_trading`, `enable_xrp_trading`
- dual-limit tuning: `dual_limit_price`, `dual_limit_shares`, `dual_limit_hedge_after_minutes`, `dual_limit_hedge_price`
- trailing tuning: `trailing_stop_point`, `dual_limit_hedge_trailing_stop`, `trailing_shares`
- behavior modes: `continuous`, `dual_limit_trailing_buy_mode`

---

## `trade.toml`

Loaded by `CopyTradingConfig::load()` from `src/copy_trading.rs`. This file drives `main_copytrading`.

### Top-level

- `clob_host` (`string`)  
  Default: `https://clob.polymarket.com`.

- `chain_id` (`number`)  
  Default: `137` (Polygon).

- `port` (`number`)  
  Web server bind port. Default: `8000`.

- `simulation` (`bool`)  
  If true, no real order execution.

### `[copy]`

- `target_address` or `target_addresses` (`string` or `array<string>`)  
  One or many leader wallets to mirror.

- `revert_trade` (`bool`)  
  When false, leader sell events are ignored.  
  When true, sells may be mirrored (subject to filters).

- `size_multiplier` (`float`)  
  Scales copied trade size.

- `poll_interval_sec` (`float`)  
  Position polling interval; supports decimals (`0.5` = 2 times/sec).

### `[filter]`

- `buy_amount_limit_in_usd` (`float`)  
  Caps copied BUY notional.

- `entry_trade_sec` (`number`)  
  Ignore stale events older than this threshold.

- `trade_sec_from_resolve` (`number`)  
  Ignore markets too close to resolution.

### `[exit]`

- `take_profit` (`float`, percent)
- `stop_loss` (`float`, percent)
- `trailing_stop` (`float`, percent)

Exit loop runs periodically and closes positions using market sell logic when thresholds are hit.

### `[ui]`

- `delta_highlight_sec` (`number`)
- `delta_animation_sec` (`number`)

Controls UI behavior for position delta highlighting/animation.

---

## `.env`

Loaded via `dotenvy`.

### AI Provider Keys

- `OPENROUTER_API_KEY`
- `OPENAI_API_KEY`
- `ANTHROPIC_API_KEY`

Only providers with keys are surfaced by `/api/agent/providers`.

### Optional Model Overrides

- `OPENROUTER_MODEL`
- `OPENAI_MODEL`
- `ANTHROPIC_MODEL`

### Optional Token Limit Controls

- `OPENROUTER_MAX_TOKENS`
- `OPENAI_MAX_TOKENS`
- `ANTHROPIC_MAX_TOKENS`

---

## Precedence Notes

- CLI flag `--simulation` on `main_copytrading` forces simulation mode.
- If no CLI simulation flag is provided, `trade.toml` `simulation` is used.
- `config.json` is always required for authenticated API operations.

---

## Example Safe Starter Profile

Use this for initial testing:

- `trade.toml`: `simulation = true`
- `[copy].size_multiplier = 0.01`
- `[filter].buy_amount_limit_in_usd = 5`
- `[exit].stop_loss = 3`
- `[exit].take_profit = 5`
- `[exit].trailing_stop = 2`

Then validate behavior in simulation before enabling live mode.
