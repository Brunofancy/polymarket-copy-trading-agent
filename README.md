````markdown
<p align="center">
  <h1 align="center">Polymarket Copy Trading Agent</h1>
  <p align="center">
    Follow Polymarket traders, manage your trading activity, and use an AI agent for faster market research from one dashboard.
  </p>
  <p align="center">
    Rust backend · Real-time Polymarket stream · Web dashboard · AI agent for trading research
  </p>
</p>

---

## Welcome

This project is a **Polymarket copy trading agent** built for people who want a smoother way to trade on **Polymarket**.

Instead of manually checking wallets, refreshing markets, and trying to react late, this project helps you handle **Polymarket trading** from one place. You can watch trader activity, copy trades, monitor positions, and use an **AI agent** to research markets before making decisions.

The goal is simple:

- make **Polymarket trading** easier
- make wallet tracking easier
- make copy trading more practical
- give users an **agent** that helps with market analysis
- keep everything fast and local

This is not just a dashboard. It is a **Polymarket trading workflow** with an integrated **agent** layer.

---

## What this project helps you do

With this **Polymarket agent**, you can:

- follow one or more traders on **Polymarket**
- copy their **trading** activity automatically
- watch live **Polymarket** trades as they happen
- review your portfolio and open positions
- test ideas in simulation mode before live **trading**
- use an AI **agent** to analyze a market, trader, or position

If you already spend time watching **Polymarket**, this project is meant to make that process more comfortable.

---

## Why this Polymarket trading agent exists

A lot of people try copy trading on **Polymarket**, but many do it in a very basic way:

- follow one wallet
- copy everything
- hope it works

That usually breaks down fast.

Good **trading** on **Polymarket** needs more than blind copying. It needs filtering, timing, sizing, and better judgment. That is why this project combines:

- real-time **Polymarket** trade tracking
- configurable copy **trading**
- portfolio monitoring
- an AI **agent** for research and context

The **agent** does not place trades for you. It helps you think more clearly about a market, a signal, or a trader before you act.

So the real purpose of this project is not reckless speed. It is better decision-making for **Polymarket trading**.

---

## What you get in the dashboard

When you run the project, you get a web interface where your **Polymarket trading** activity is easier to understand.

### Dashboard
See what is happening right now in your **Polymarket** setup. This is the fastest place to monitor live copy **trading**, recent actions, and important changes.

### Agent
The AI **agent** helps with **Polymarket** market research. Ask about a market, a trader, a position, or a recent move, and the **agent** gives you structured trading context.

### Logs
Review the full history of events and **trading** actions in real time.

### Top Traders
Track the wallets you care about most on **Polymarket** and watch their activity as it happens.

### Portfolio
See your current positions, exposure, and active **trading** state in one place.

### Settings
Adjust how your copy **trading agent** behaves, including sizing, filters, and exit logic.

---

## Screenshots

| Dashboard | Agent | Logs |
|:---:|:---:|:---:|
| ![Dashboard](docs/screenshots/dashboard.png) | ![Agent](docs/screenshots/agent.png) | ![Logs](docs/screenshots/logs.png) |

| Top Traders | Portfolio | Settings |
|:---:|:---:|:---:|
| ![Top Traders](docs/screenshots/toptraders.png) | ![Portfolio](docs/screenshots/portfolio.png) | ![Settings](docs/screenshots/settings.png) |

---

## Getting started

You do not need to understand the whole codebase before using this **Polymarket trading agent**.

Just follow the setup below.

### 1) Clone the project

```bash
git clone https://github.com/brunobmtx/polymarket-copy-trading-bot-agent.git
cd polymarket-copy-trading-bot-agent
````

---

### 2) Add your Polymarket credentials

Create a `config.json` file in the project root.

This file is for your **Polymarket** CLOB credentials and wallet setup.

```jsonc
{
  "polymarket": {
    "gamma_api_url": "https://gamma-api.polymarket.com",
    "clob_api_url": "https://clob.polymarket.com",
    "api_key": "your-api-key",
    "api_secret": "your-api-secret",
    "api_passphrase": "your-api-passphrase",
    "private_key": "your-private-key",
    "proxy_wallet_address": null,
    "signature_type": 0
  }
}
```

This is the core connection between your local setup and **Polymarket trading**.

---

### 3) Define how your copy trading should behave

Create a `trade.toml` file in the project root.

This file tells the **agent** which **Polymarket** trader to follow and how your **trading** rules should work.

```toml
[copy]
target_address = "0x1979ae6B7E6534dE9c4539D0c205E582cA637C9D"
# or target_addresses = ["0x...", "0x..."]
size_multiplier = 0.01
poll_interval_sec = 0.5

[exit]
take_profit = 0
stop_loss = 0
trailing_stop = 0

[filter]
buy_amount_limit_in_usd = 0
entry_trade_sec = 0
trade_sec_from_resolve = 0
```

A few important notes:

* `target_address` is the **Polymarket** wallet you want to copy
* `size_multiplier` controls how much of their trade size you mirror
* exit values set your **trading** protection rules
* filter values help you avoid low-quality copy **trading** behavior

---

### 4) Add AI agent keys if you want agent research

The AI **agent** is optional, but it makes the **Polymarket trading** workflow much more useful.

Create a `.env` file if you want to use the **agent**:

```env
OPENROUTER_API_KEY=sk-or-...
# or OPENAI_API_KEY=sk-...
# or ANTHROPIC_API_KEY=sk-ant-...
```

Without this, the copy **trading** features still work.
With this, the **agent** tab becomes available for research.

---

### 5) Build and run

```bash
cd frontend && trunk build --release && cd ..
cargo run --release --bin main_copytrading
```

Then open:

```text
http://localhost:8000
```

At that point, your **Polymarket agent** dashboard should be live.

---

## Want to test first without real trading?

Use simulation mode.

```bash
cargo run --release --bin main_copytrading -- --simulation
```

This is the best way to get comfortable with the **Polymarket trading agent** before live use.

Simulation mode is useful for:

* testing your setup
* checking your copy **trading** configuration
* reviewing trader behavior
* exploring the dashboard
* using the **agent** safely before real execution

---

## What you need before using it

Before running this **Polymarket** project, make sure you have:

* **Rust 1.70+**
* a **Polymarket** account
* USDC on Polygon
* **Polymarket** CLOB API credentials
* frontend tooling:

  * `cargo install trunk`
  * `rustup target add wasm32-unknown-unknown`

---

## How the Polymarket trading flow works

This **agent** listens to the live **Polymarket** activity WebSocket.

When a target wallet makes a trade, the project sees that event, checks your rules, and decides whether to copy the **trading** action. At the same time, it updates the dashboard so you can see what is happening.

Basic flow:

```text
Polymarket activity stream
        ↓
filter by target wallet
        ↓
apply trading rules
        ↓
copy trade or ignore
        ↓
show result in dashboard, logs, and portfolio
```

So the project is always centered around real-time **Polymarket trading**.

---

## Screenshots

| Dashboard | Agent | Logs |
|:---:|:---:|:---:|
| ![Dashboard](docs/screenshots/dashboard.png) | ![Agent](docs/screenshots/agent.png) | ![Logs](docs/screenshots/logs.png) |

| Top Traders | Portfolio | Settings |
|:---:|:---:|:---:|
| ![Top Traders](docs/screenshots/toptraders.png) | ![Portfolio](docs/screenshots/portfolio.png) | ![Settings](docs/screenshots/settings.png) |

---

## How the AI agent helps

The AI **agent** is for research, not automatic execution.

You can ask the **agent** things like:

* Is this **Polymarket** market worth entering?
* Why is this trader buying here?
* What are the risks of this position?
* What changed in this market?
* Is this move likely late for copy **trading**?

The **agent** can help organize your thinking around:

* market sentiment
* catalysts
* timing
* confidence
* possible risks
* overall **trading** context

This makes the **agent** useful as a research assistant inside your **Polymarket** workflow.

---

## A simple explanation of the main config

### `config.json`

This handles your **Polymarket** credentials and wallet settings.

Important fields:

* `clob_api_url` → usually `https://clob.polymarket.com`
* `private_key` → your Polygon wallet private key
* `api_key`, `api_secret`, `api_passphrase` → your **Polymarket** API credentials
* `proxy_wallet_address` → optional
* `signature_type` → wallet signing mode

### `trade.toml`

This controls your copy **trading** behavior.

Important areas:

* `[copy]` → who you follow and how much you copy
* `[exit]` → risk controls for **trading**
* `[filter]` → filters that reduce bad copy **trading**
* `[ui]` → dashboard behavior

---

## Running it in production

To deploy this **Polymarket trading agent** on a server:

```bash
cd frontend && trunk build --release && cd ..
cargo run --release --bin main_copytrading
```

You can then access it from another device on your network with:

```text
http://<your-server-ip>:8000
```

This project serves the UI and backend together, so the setup is relatively simple for a **Polymarket** tool.

---

## Project structure

If you want to explore the code, here is the easiest mental model:

### Main entry

* `src/bin/main_copytrading.rs`
  Starts the app, serves the dashboard, and exposes **agent** endpoints

### Polymarket API and core setup

* `src/api.rs`
  **Polymarket** API client
* `src/clob_sdk.rs`
  CLOB SDK integration
* `src/config.rs`
  CLI args and config loading
* `src/models.rs`
  Shared data models for **trading** and market state

### Trading logic

* `src/copy_trading.rs`
  Copy **trading** logic, filters, exits, and config
* `src/trader.rs`
  Order handling and portfolio updates
* `src/simulation.rs`
  Simulation mode
* `src/backtest.rs`
  Backtest logic

### Monitoring

* `src/activity_stream.rs`
  Real-time **Polymarket** activity stream
* `src/detector.rs`
  Opportunity detection
* `src/monitor.rs`
  Market monitoring
* `src/rtds.rs`
  External price feed support

### Web state and UI

* `src/web_state.rs`
  Dashboard state and API output
* `src/logging.rs`
  Trade and system logs
* `frontend/`
  The web dashboard for **Polymarket trading** and the AI **agent**

---

## Who this project is for

This project is a good fit if you are:

* active on **Polymarket**
* interested in copy **trading**
* trying to monitor multiple traders
* building a better **trading** workflow
* looking for a local-first **agent** tool
* interested in combining AI **agent** research with **Polymarket**

It is especially useful if you want faster execution and a more organized **trading** process.

---

## References

* [Polymarket CLOB Documentation](https://docs.polymarket.com/developers/CLOB/)
* [Polymarket API Reference](https://docs.polymarket.com/api-reference/introduction)
* [OpenRouter](https://openrouter.ai/)
* [Mahoraga](https://mahoraga.dev/)

---

**Contact:** [@snipmaxi](https://t.me/snipmaxi)