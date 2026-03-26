---
name: weex-trader
description: "WEEX交易所交易+加密货币全市场分析。触发词：crypto/币/BTC/ETH/SOL/DOGE/PEPE/合约/现货/下单/持仓/资金费率/K线/深度/行情/价格/杠杆/做多做空/meme币/DeFi/RSI/Gas/恐惧贪婪/funding/orderbook/klines/ticker。⛔禁止WebSearch/WebFetch/curl查币价！✅用快捷命令（S=$HOME/.claude/skills/weex-trader/scripts）：现货余额→python $S/weex_spot_api.py balance --pretty；合约余额→python $S/weex_contract_api.py balance --pretty；持仓→python $S/weex_contract_api.py positions --pretty；现货价格→python $S/weex_spot_api.py ticker --symbol BTCUSDT --pretty；合约价格→python $S/weex_contract_api.py ticker --symbol BTCUSDT --pretty；K线→python $S/weex_contract_api.py klines --symbol BTCUSDT --interval 1d --limit 14 --pretty；深度→python $S/weex_contract_api.py depth --symbol BTCUSDT --pretty；资金费率→python $S/weex_contract_api.py funding --symbol BTCUSDT --pretty；费率历史→python $S/weex_contract_api.py funding-history --symbol BTCUSDT --limit 21 --pretty；24h行情→python $S/weex_contract_api.py ticker24h --symbol BTCUSDT --pretty；持仓量→python $S/weex_contract_api.py open-interest --symbol BTCUSDT --pretty；成交→python $S/weex_contract_api.py trades --symbol BTCUSDT --pretty；恐惧贪婪→bash $S/crypto.sh fear；现货下单→python $S/weex_spot_api.py place-order --symbol ETHUSDT --side BUY --order-type MARKET --quantity 0.05 --dry-run --pretty"
metadata:
  version: "1.0.0"
  author: HorizonGazer
  homepage: https://github.com/HorizonGazer/weex-trader-skill
---

# WEEX Trader Skill

## 🚫 HARD CONSTRAINT — READ THIS FIRST

**This skill has EXACTLY 3 scripts. No MCP servers. No imaginary tools. No hallucinated subcommands.**
**Do NOT use WebSearch, WebFetch, or curl. Use the scripts below.**
**Do NOT invent subcommand names. ONLY use subcommands listed in this file.**

## ⚡ SHORTCUT COMMANDS — USE THESE (NOT `call --endpoint`)

**ALWAYS set S first:** `S=$HOME/.claude/skills/weex-trader/scripts`

### Spot API shortcuts (`weex_spot_api.py`)

| Subcommand | Example | Auth |
|------------|---------|------|
| **`ticker`** | `python $S/weex_spot_api.py ticker --symbol BTCUSDT --pretty` | No |
| **`balance`** | `python $S/weex_spot_api.py balance --pretty` | Yes |
| **`depth`** | `python $S/weex_spot_api.py depth --symbol BTCUSDT --limit 15 --pretty` | No |
| **`klines`** | `python $S/weex_spot_api.py klines --symbol BTCUSDT --interval 1d --limit 14 --pretty` | No |
| **`trades`** | `python $S/weex_spot_api.py trades --symbol BTCUSDT --limit 50 --pretty` | No |
| **`place-order`** | `python $S/weex_spot_api.py place-order --symbol ETHUSDT --side BUY --order-type MARKET --quantity 0.05 --dry-run --pretty` | Yes |
| `call` | Generic fallback: `python $S/weex_spot_api.py call --endpoint spot.xxx --query '{...}' --pretty` | Varies |
| `list-endpoints` | `python $S/weex_spot_api.py list-endpoints --pretty` | No |

### Contract API shortcuts (`weex_contract_api.py`)

| Subcommand | Example | Auth |
|------------|---------|------|
| **`ticker`** | `python $S/weex_contract_api.py ticker --symbol BTCUSDT --pretty` | No |
| **`balance`** | `python $S/weex_contract_api.py balance --pretty` | Yes |
| **`positions`** | `python $S/weex_contract_api.py positions --pretty` (all) or `positions --symbol BTCUSDT --pretty` (one) | Yes |
| **`depth`** | `python $S/weex_contract_api.py depth --symbol BTCUSDT --limit 15 --pretty` | No |
| **`klines`** | `python $S/weex_contract_api.py klines --symbol BTCUSDT --interval 1d --limit 14 --pretty` | No |
| **`funding`** | `python $S/weex_contract_api.py funding --symbol BTCUSDT --pretty` | No |
| **`funding-history`** | `python $S/weex_contract_api.py funding-history --symbol BTCUSDT --limit 21 --pretty` | No |
| **`trades`** | `python $S/weex_contract_api.py trades --symbol BTCUSDT --limit 50 --pretty` | No |
| **`ticker24h`** | `python $S/weex_contract_api.py ticker24h --symbol BTCUSDT --pretty` | No |
| **`open-interest`** | `python $S/weex_contract_api.py open-interest --symbol BTCUSDT --pretty` | No |
| **`place-order`** | `python $S/weex_contract_api.py place-order --symbol BTCUSDT --side BUY --position-side LONG --type MARKET --quantity 0.001 --dry-run --pretty` | Yes |
| **`cancel-order`** | `python $S/weex_contract_api.py cancel-order --order-id 12345 --confirm-live --pretty` | Yes |
| `call` | Generic fallback: `python $S/weex_contract_api.py call --endpoint market.xxx --query '{...}' --pretty` | Varies |
| `list-endpoints` | `python $S/weex_contract_api.py list-endpoints --pretty` | No |

### Market Analysis CLI (`crypto.sh`)

| Command | Function |
|---------|----------|
| `bash $S/crypto.sh fear` | Fear & Greed Index |
| `bash $S/crypto.sh weex BTCUSDT ETHUSDT` | WEEX spot tickers |
| `bash $S/crypto.sh weex-futures BTCUSDT` | Futures + funding |
| `bash $S/crypto.sh weex-depth BTCUSDT` | Order book depth |
| `bash $S/crypto.sh weex-funding BTCUSDT` | Funding history |
| `bash $S/crypto.sh price bitcoin ethereum` | CoinGecko prices |
| `bash $S/crypto.sh market` | Global market overview |
| `bash $S/crypto.sh trending` | Trending coins |
| `bash $S/crypto.sh rsi bitcoin` | RSI indicator |
| `bash $S/crypto.sh ma bitcoin` | Moving averages |
| `bash $S/crypto.sh defi` | DeFi TVL ranking |
| `bash $S/crypto.sh memes` | Meme coins |
| `bash $S/crypto.sh gas` | ETH gas tracker |
| `bash $S/crypto.sh info bitcoin` | Coin details |
| `bash $S/crypto.sh compare bitcoin ethereum` | Compare coins |

---

## ⚠️ COMMON MISTAKES — DO NOT MAKE THESE

| WRONG (will fail) | CORRECT |
|---|---|
| `weex_spot_api.py account` | `weex_spot_api.py balance` |
| `weex_spot_api.py place-order --type MARKET` | `weex_spot_api.py place-order --order-type MARKET` |
| `weex_spot_api.py place-order --quoteOrderQty 100` | Not supported. Calculate quantity manually: qty = 100 / price |
| `weex_contract_api.py call --endpoint account.get_account_info` | `weex_contract_api.py balance` |
| `weex_contract_api.py call --endpoint market.get_depth` | `weex_contract_api.py depth --symbol X` |
| `weex_contract_api.py call --endpoint market.get_funding_rate` | `weex_contract_api.py funding --symbol X` |
| `klines --interval 1D` (uppercase) | `klines --interval 1d` (lowercase) |
| `depth --limit 20` | `depth --limit 15` (only 15 or 200) |

### Spot place-order parameter name: `--order-type` (NOT `--type`)
### Contract place-order parameter name: `--type` (NOT `--order-type`)

---

## ⚡ API Priority Rule

**WEEX API FIRST — ALWAYS.** Only fall back to CoinGecko/DefiLlama/Alternative.me when WEEX cannot provide the data (Fear & Greed, DeFi TVL, trending, meme ranking, coin info, gas).

## Bootstrap — .env Auto-Load (Built-in)

Both Python scripts **automatically load `.env` at startup**. No manual `source` needed.

Resolution order (first found wins):
1. `$HOME/.claude/skills/weex-trader/.env` (user-level)
2. `.claude/skills/weex-trader/.env` (project-level)
3. `.env` (current directory)

If `.env` exists with `WEEX_API_KEY`, `WEEX_API_SECRET`, `WEEX_API_PASSPHRASE` — private endpoints work automatically. Never prompt for credentials when `.env` is configured.

---

## Safety Policy — CODE-ENFORCED TWO-STEP VERIFICATION

**ALL trading operations (place-order, cancel-order) are CODE-LEVEL ENFORCED with proof tokens.**
**`--confirm-live` will be REJECTED by the script if `--dry-run` was not run first with the same parameters.**

### How It Works (Proof Token Mechanism)

1. `--dry-run` writes a proof token file (SHA-256 fingerprint of parameters) to temp directory
2. `--confirm-live` checks for a matching, non-expired (5 min TTL) proof token
3. If no matching token → **script exits with error**, order is NOT placed
4. After successful execution → token is consumed (deleted), preventing replay

### Step 1: Dry-Run Preview (ALWAYS do this first)

```bash
S=$HOME/.claude/skills/weex-trader/scripts

# Spot — preview + generate proof token
python $S/weex_spot_api.py place-order \
  --symbol ETHUSDT --side BUY --order-type LIMIT \
  --quantity 0.05 --price 2000 --time-in-force GTC --dry-run --pretty
# Output includes "proof_token" path and "proof_ttl_seconds": 300

# Contract — preview + generate proof token
python $S/weex_contract_api.py place-order \
  --symbol BTCUSDT --side BUY --position-side LONG --type MARKET \
  --quantity 0.001 --dry-run --pretty
```

Present the dry-run output to the user and **explicitly ask for confirmation**.

### Step 2: Live Execution (ONLY after user confirms)

Only after the user explicitly says "yes" / "confirm" / "go ahead":

```bash
# Replace --dry-run with --confirm-live (proof token must exist from step 1)
python $S/weex_spot_api.py place-order \
  --symbol ETHUSDT --side BUY --order-type LIMIT \
  --quantity 0.05 --price 2000 --time-in-force GTC --confirm-live --pretty
```

### Rules
- **NEVER skip dry-run** — even if the user says "buy now", ALWAYS show dry-run first
- **NEVER add `--confirm-live` without prior user confirmation**
- **Skipping dry-run is IMPOSSIBLE** — the script will reject `--confirm-live` without a matching proof token
- If fields are missing, ask BEFORE dry-run
- After dry-run, present clear summary: symbol, side, type, quantity, price, estimated cost
- For "buy 100U of ETH" style requests: get current price first, calculate quantity = 100 / price, then dry-run
- Proof token expires after 5 minutes — re-run `--dry-run` if expired

---

## Professional Tips

- Fear & Greed Index: <20 Extreme Fear = buy signal, >80 Extreme Greed = sell signal
- Technical: RSI>70 overbought, RSI<30 oversold
- Funding Rate: sustained negative = shorts dominant, sustained positive = longs dominant
- DCA Strategy: regular fixed-amount investing, don't try to time the market

## Data Sources (by priority)

1. **🥇 WEEX Exchange (PRIMARY)** — Use first for any price/market/trading query
2. **🥈 CoinGecko** — trends, meme ranking, coin info (supplementary)
3. **🥈 DefiLlama** — DeFi TVL, yield (supplementary)
4. **🥈 Alternative.me** — Fear & Greed Index (supplementary)

## References

- `references/spot-api-definitions.json` / `references/contract-api-definitions.json`
- `references/auth-and-signing.md` / `references/websocket.md`
- `docs/guide.md` — 新手入门 / `docs/strategies.md` — 投资策略 / `docs/tips.md` — 技巧速查

---
*weex-trader v1.0.0 by HorizonGazer — WEEX Trading + Market Analysis unified*
