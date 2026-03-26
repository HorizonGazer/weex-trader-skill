# 🔮 WEEX Trader Skill

> AI-powered crypto trading & market analysis for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) / [Codex](https://openai.com/codex) / [Openclaw](https://openclaw.com)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.2.0-green.svg)](#changelog)
[![WEEX API](https://img.shields.io/badge/WEEX-V3_API-orange.svg)](https://www.weex.com)
[![Safety](https://img.shields.io/badge/Trade_Safety-Proof_Token-red.svg)](#-innovation-1-proof-token-trade-safety)

Trade futures & spot on WEEX exchange, analyze crypto markets, and manage your portfolio — all through natural language in your AI coding agent.

> **Fork of [drgnchan/weex-trader-skill](https://github.com/drgnchan/weex-trader-skill)** — enhanced with proof-token trade safety, multi-source market analysis, and anti-hallucination agent engineering.

---

## 🆕 What's Different in This Fork?

This fork solves **three real problems** discovered during production use of the original skill:

### 🔒 Innovation 1: Proof Token Trade Safety

**Problem:** AI agents can skip `--dry-run` and execute `--confirm-live` directly — no code-level enforcement existed. In testing, the agent placed a real $100 trade without asking for confirmation.

**Solution:** A **SHA-256 proof token mechanism** baked into the script itself:

```
  --dry-run  →  generates proof token (SHA-256 of order params)
                token saved to temp dir, 5-min TTL, single-use
                    │
          user confirms ("yes" / "go ahead")
                    │
  --confirm-live  →  script checks for matching token
                     ❌ no token / expired / wrong params → ORDER REJECTED
                     ✅ valid token → execute, then delete token
```

**This is not a prompt-level rule — it's code-level enforcement.** Even if an agent ignores SKILL.md instructions, the Python script will refuse to place the order. No token, no trade. Period.

### 🧠 Innovation 2: Anti-Hallucination Agent Engineering

**Problem:** AI agents hallucinate subcommands (`account` instead of `balance`), wrong flags (`--type` instead of `--order-type`), and fabricated endpoint names (`account.get_account_info`).

**Solution:**
- **13 shortcut commands** embedded directly in the SKILL.md `description` field — agents copy-paste, no room for invention
- **8-entry common mistakes table** with wrong → correct mapping
- **Hard constraints** at the top of SKILL.md: "EXACTLY 3 scripts. No MCP servers. No imaginary tools."
- Parameter name differences called out explicitly: Spot = `--order-type`, Futures = `--type`

### 🌐 Innovation 3: Multi-Source Market Intelligence

**Problem:** The original skill only queries WEEX exchange data. No way to check Fear & Greed, DeFi TVL, meme coin rankings, RSI, or gas fees.

**Solution:** `crypto.sh` — a 15-command CLI that aggregates 4 data sources with clear priority rules:

| Priority | Source | Data |
|----------|--------|------|
| 🥇 Primary | **WEEX API** | Prices, depth, klines, funding, positions, orders |
| 🥈 Supplementary | CoinGecko | Trending, meme ranking, coin info, price comparison |
| 🥈 Supplementary | DefiLlama | DeFi TVL, yield data |
| 🥈 Supplementary | Alternative.me | Fear & Greed Index |

---

## ✨ Features

| Category | Capabilities |
|----------|-------------|
| **🔄 Spot Trading** | Market/Limit orders, balance queries, order management |
| **📈 Futures Trading** | Long/Short positions, leverage, funding rates, position management |
| **📊 Market Data** | Tickers, K-lines, order book depth, 24h stats, open interest |
| **🧠 Market Analysis** | Fear & Greed Index, RSI, Moving Averages, DeFi TVL, trending coins |
| **🔒 Proof Token Safety** | Code-enforced two-step verification — agents can't bypass it |
| **🌐 Multi-Source** | WEEX (primary) + CoinGecko + DefiLlama + Alternative.me |

## 🚀 Quick Start

### 1. Install in Claude Code

```bash
claude skill add --url https://github.com/HorizonGazer/weex-trader-skill
```

### 2. Install in Codex / Openclaw

Say to your agent:

```
Help me install this skill: https://github.com/HorizonGazer/weex-trader-skill
```

### 3. Set Up API Credentials (for trading)

> **Not needed** for public market data. Only required for account/trading operations.

Create an API key at [WEEX API Management](https://www.weex.com/account/newapi/), then add to your shell profile (`~/.bashrc`, `~/.zshrc`, or `.env` file):

```bash
export WEEX_API_KEY="your-api-key"
export WEEX_API_SECRET="your-secret-key"
export WEEX_API_PASSPHRASE="your-passphrase"
export WEEX_API_BASE="https://api-contract.weex.com"
export WEEX_LOCALE="en-US"
```

Or create a `.env` file at `~/.claude/skills/weex-trader/.env` (auto-loaded by scripts).

### 4. Start Using

Just talk naturally:

```
What's the current BTC price?
Show me ETH funding rate history
Buy 0.05 ETH at market price
Compare BTC and SOL performance
What's the Fear & Greed Index?
```

---

## 📖 Command Reference

### Spot API (`weex_spot_api.py`)

```bash
S=$HOME/.claude/skills/weex-trader/scripts

python $S/weex_spot_api.py ticker --symbol BTCUSDT --pretty          # Price
python $S/weex_spot_api.py balance --pretty                          # Account balance
python $S/weex_spot_api.py depth --symbol ETHUSDT --limit 15 --pretty # Order book
python $S/weex_spot_api.py klines --symbol BTCUSDT --interval 1d --limit 14 --pretty
python $S/weex_spot_api.py trades --symbol BTCUSDT --limit 50 --pretty
python $S/weex_spot_api.py list-endpoints --pretty                   # All available endpoints
```

### Futures API (`weex_contract_api.py`)

```bash
python $S/weex_contract_api.py ticker --symbol BTCUSDT --pretty       # Price
python $S/weex_contract_api.py balance --pretty                       # Account balance
python $S/weex_contract_api.py positions --pretty                     # All positions
python $S/weex_contract_api.py depth --symbol BTCUSDT --limit 15 --pretty
python $S/weex_contract_api.py klines --symbol BTCUSDT --interval 1d --limit 14 --pretty
python $S/weex_contract_api.py funding --symbol BTCUSDT --pretty      # Current funding rate
python $S/weex_contract_api.py funding-history --symbol BTCUSDT --limit 21 --pretty
python $S/weex_contract_api.py ticker24h --symbol BTCUSDT --pretty    # 24h statistics
python $S/weex_contract_api.py open-interest --symbol BTCUSDT --pretty
python $S/weex_contract_api.py trades --symbol BTCUSDT --limit 50 --pretty
```

### Market Analysis (`crypto.sh`) — *New in this fork*

```bash
bash $S/crypto.sh fear                        # Fear & Greed Index
bash $S/crypto.sh weex BTCUSDT ETHUSDT        # WEEX spot tickers
bash $S/crypto.sh weex-futures BTCUSDT        # Futures + funding
bash $S/crypto.sh weex-depth BTCUSDT          # Order book depth
bash $S/crypto.sh price bitcoin ethereum      # CoinGecko prices
bash $S/crypto.sh market                      # Global market overview
bash $S/crypto.sh trending                    # Trending coins
bash $S/crypto.sh rsi bitcoin                 # RSI indicator
bash $S/crypto.sh ma bitcoin                  # Moving averages
bash $S/crypto.sh defi                        # DeFi TVL ranking
bash $S/crypto.sh memes                       # Meme coins
bash $S/crypto.sh gas                         # ETH gas tracker
bash $S/crypto.sh info bitcoin                # Coin details
bash $S/crypto.sh compare bitcoin ethereum    # Compare coins
```

---

## 🔒 Safety: Two-Step Trade Verification — *New in this fork*

All trading operations are **code-enforced** with a proof token mechanism. Even if the AI agent tries to skip the safety check, the script itself will reject the order.

### How It Works

```
┌─────────────────────────────────────────────────────────┐
│  Step 1: --dry-run                                      │
│  → Script generates SHA-256 proof token                 │
│  → Shows order preview to user                          │
│  → Token stored in temp dir (5 min TTL)                 │
├─────────────────────────────────────────────────────────┤
│  🧑 User reviews and confirms ("yes" / "go ahead")      │
├─────────────────────────────────────────────────────────┤
│  Step 2: --confirm-live                                 │
│  → Script verifies matching proof token exists          │
│  → No token / expired → ❌ ORDER REJECTED               │
│  → Valid token → ✅ Order executed, token consumed       │
└─────────────────────────────────────────────────────────┘
```

### Example: Spot Market Buy

```bash
# Step 1: Preview (generates proof token)
python $S/weex_spot_api.py place-order \
  --symbol ETHUSDT --side BUY --order-type MARKET \
  --quantity 0.05 --dry-run --pretty

# User confirms → Step 2: Execute
python $S/weex_spot_api.py place-order \
  --symbol ETHUSDT --side BUY --order-type MARKET \
  --quantity 0.05 --confirm-live --pretty
```

### Example: Futures Limit Short

```bash
# Step 1: Preview
python $S/weex_contract_api.py place-order \
  --symbol BTCUSDT --side SELL --position-side SHORT --type LIMIT \
  --quantity 0.001 --price 100000 --time-in-force GTC --dry-run --pretty

# User confirms → Step 2: Execute
python $S/weex_contract_api.py place-order \
  --symbol BTCUSDT --side SELL --position-side SHORT --type LIMIT \
  --quantity 0.001 --price 100000 --time-in-force GTC --confirm-live --pretty
```

> **Key guarantees:**
> - `--confirm-live` without prior `--dry-run` → **script rejects** (no valid token)
> - Token expires after 5 minutes → must re-run `--dry-run`
> - Each token is single-use → no replay attacks

---

## 🏗️ Project Structure

```
weex-trader-skill/
├── SKILL.md                  # Skill definition (agent instructions)
├── README.md                 # This file
├── LICENSE                   # MIT License
├── .env                      # API credentials (git-ignored)
├── .gitignore
├── scripts/
│   ├── weex_spot_api.py      # Spot REST API client (+ proof token)
│   ├── weex_contract_api.py  # Futures REST API client (+ proof token)
│   ├── crypto.sh             # Market analysis CLI (multi-source) ← NEW
│   ├── generate_weex_api_definitions.py  # API definition generator
│   └── skill_update.py       # Self-update tool
├── references/
│   ├── spot-api-definitions.json     # Machine-readable spot API
│   ├── spot-api-definitions.md       # Human-readable spot API
│   ├── contract-api-definitions.json # Machine-readable futures API
│   ├── contract-api-definitions.md   # Human-readable futures API
│   ├── spot-endpoints.md
│   ├── contract-endpoints.md
│   ├── auth-and-signing.md           # Authentication guide
│   ├── websocket.md                  # WebSocket reference
│   └── updates.md
└── docs/                             ← NEW
    ├── guide.md              # Beginner's guide (新手入门)
    ├── strategies.md         # Investment strategies (投资策略)
    ├── tips.md               # Quick tips (技巧速查)
    ├── CAPABILITY_DEMO.md    # Feature demonstration
    └── TEST_REPORT.md        # Test results
```

---

## 🔑 Supported Platforms

| Platform | Install Method |
|----------|---------------|
| **Claude Code** | `claude skill add --url https://github.com/HorizonGazer/weex-trader-skill` |
| **Codex** | Natural language: "Install this skill: https://github.com/HorizonGazer/weex-trader-skill" |
| **Openclaw** | Search "weex-trader-skill" in marketplace |

---

## ⚙️ API Endpoints

This skill wraps the full WEEX V3 REST API:

| Product | Order Endpoint | Data Endpoints |
|---------|---------------|----------------|
| **Spot** | `POST /api/v3/order` | `/api/v1/ticker`, `/api/v1/depth`, `/api/v1/klines`, ... |
| **Futures** | `POST /capi/v3/order` | `/capi/v1/ticker`, `/capi/v1/depth`, `/capi/v1/klines`, ... |

Full endpoint list: run `python scripts/weex_spot_api.py list-endpoints --pretty` or `python scripts/weex_contract_api.py list-endpoints --pretty`.

---

## 🔧 Advanced Usage

### Regenerate API Definitions

Pull the latest endpoint definitions from WEEX docs:

```bash
python scripts/generate_weex_api_definitions.py --product all
```

### Self-Update

```bash
python scripts/skill_update.py check --repo HorizonGazer/weex-trader-skill
python scripts/skill_update.py update --repo HorizonGazer/weex-trader-skill
```

### Generic Endpoint Call

For endpoints without a shortcut command:

```bash
# Spot
python $S/weex_spot_api.py call --endpoint spot.xxx --query '{"key":"value"}' --pretty

# Futures
python $S/weex_contract_api.py call --endpoint market.xxx --query '{"key":"value"}' --pretty
```

---

## 🛡️ Security Notes

- **Never commit** `.env` or API credentials (`.env` is in `.gitignore`)
- Use **least-privilege** API keys — enable only the permissions you need
- If credentials are compromised, **revoke immediately** at [WEEX API Management](https://www.weex.com/account/newapi/)
- Proof token system prevents AI agents from executing trades without user confirmation
- All proof tokens are **single-use** and expire after **5 minutes**

---

## 📋 Troubleshooting

| Problem | Solution |
|---------|----------|
| Skill not found | Reinstall: `claude skill add --url https://github.com/HorizonGazer/weex-trader-skill` |
| Authentication error | Verify `WEEX_API_KEY`, `WEEX_API_SECRET`, `WEEX_API_PASSPHRASE` in `.env` or shell |
| Order rejected | Check account balance, API key permissions, and symbol availability |
| `--confirm-live` rejected | Run `--dry-run` first to generate proof token, then retry within 5 minutes |
| `--order-type` vs `--type` | Spot uses `--order-type`, Futures uses `--type` — don't mix them up |

---

## 📜 Changelog

### v1.2.0 (2026-03-27) — Proof Token Safety
- 🔒 **Code-enforced two-step trade verification** with proof tokens (SHA-256 fingerprint)
- Scripts reject `--confirm-live` without prior `--dry-run` at code level
- Proof tokens: single-use, 5-minute TTL, tamper-proof

### v1.1.0 — Anti-Hallucination
- 🧠 Replaced `call --endpoint` with 13 shortcut commands in SKILL.md description
- ⚠️ Added common mistakes reference table (8 frequent agent errors)
- 🛡️ Introduced `--dry-run` → user confirm → `--confirm-live` safety flow

### v1.0.0 — Multi-Source Intelligence
- 🌐 Added `crypto.sh` for multi-source market analysis (CoinGecko, DefiLlama, Alternative.me)
- 📊 RSI, Moving Averages, DeFi TVL, Fear & Greed, Gas tracker
- 📖 Added `docs/`: beginner guide, investment strategies, quick tips
- 🍴 Forked from [drgnchan/weex-trader-skill](https://github.com/drgnchan/weex-trader-skill) v1.6.0 (WEEX V3 API, spot + futures)

---

## 📄 License

[MIT](LICENSE) © 2026 [HorizonGazer](https://github.com/HorizonGazer)

## 👥 Contributors

1. **[HorizonGazer](https://github.com/HorizonGazer)** — Proof token safety system, anti-hallucination agent engineering, multi-source market analysis CLI, docs & strategy guides
2. **[drgnchan](https://github.com/drgnchan)** — Original skill creator, WEEX V3 API wrappers & skill architecture

---

<p align="center">
  Built with ❤️ for the crypto trading community<br>
  <strong>WEEX Trader Skill</strong> — Trade smarter, trade safer
</p>
