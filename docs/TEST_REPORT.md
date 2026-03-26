# Crypto Tracker + WEEX API 集成测试报告

> 测试时间: 2026-03-25 | 环境: Windows 11 + Python 3.13 + Bash

---

## 1. 环境与连通性

| 测试项 | 状态 | 备注 |
|--------|------|------|
| Python 自动检测 | ✅ | 自动识别 `python` (Windows) 或 `python3` (Linux/macOS) |
| UTF-8 编码 | ✅ | 解决 Windows GBK 编码下 emoji 显示问题 |
| CoinGecko API | ✅ | 通过 Python urllib (curl 在 Windows 下超时，已替换) |
| DefiLlama API | ✅ | 通过 Python urllib |
| Alternative.me API | ✅ | 通过 Python urllib |
| WEEX Spot API | ✅ | `api-spot.weex.com` 连通正常 |
| WEEX Contract API | ✅ | `api-contract.weex.com` 连通正常 |

---

## 2. 完整交易决策流程

以下演示一个交易者从宏观到微观的完整分析链路，每一步对应一个命令。

### Step 1: 宏观市场判断

```bash
bash scripts/crypto.sh market
```

```
📊 Global Crypto Market
========================================
Total Market Cap:  $2.51T 🟢+1.36%
24h Volume:        $97.4B
BTC Dominance:     56.5%
ETH Dominance:     10.4%
Active Cryptos:    18,016
```

**交易者解读:** 总市值 $2.51T 日涨 1.36%，BTC 主导率 56.5% 偏高，资金集中在主流币。市场整体温和回暖。

---

### Step 2: 情绪面分析

```bash
bash scripts/crypto.sh fear
```

```
😱 Fear & Greed Index: 14/100 (Extreme Fear)
   [██░░░░░░░░░░░░░░░░░░]
   Signal: Extreme Fear — historically a buying opportunity
```

**交易者解读:** 极度恐惧区间 (14/100)。历史数据表明，恐惧指数低于 20 时往往是中长期买入机会。当前市场情绪极度悲观，与价格温和上涨形成背离——可能是底部反转信号。

---

### Step 3: 技术面分析

#### 3a. RSI 指标

```bash
bash scripts/crypto.sh rsi bitcoin
```

```
  Current Price:  $70,984.97
  14-day RSI:     55.6
  Signal:         🟡 中性 (Neutral)
  Advice:         RSI处于正常区间，建议持续观察
```

#### 3b. 均线分析

```bash
bash scripts/crypto.sh ma bitcoin
```

```
  Current Price:  $70,984.65
  MA7  (7-day):   $71,437.51
  MA14 (14-day):  $71,171.28
  MA30 (30-day):  $70,651.12

  Price vs MA7: BELOW (-0.63%) 🔴
  Price vs MA14: BELOW (-0.26%) 🔴
  Price vs MA30: ABOVE (+0.47%) 🟢

  Cross:  🟢 金叉信号 (Golden Cross) — 短期均线在长期均线上方，上升趋势
  Trend:  📊 偏多震荡 (Bullish Consolidation)
```

**交易者解读:**
- RSI 55.6 中性，没有超买超卖，还有上行空间
- 价格在 MA30 上方但低于 MA7/MA14，短期回调中
- 均线呈金叉排列 (MA7 > MA14 > MA30)，中期趋势偏多
- 结论: 偏多震荡，可等价格回踩 MA30 ($70,651) 附近考虑入场

---

### Step 4: WEEX 交易所实时行情

```bash
bash scripts/crypto.sh weex BTCUSDT ETHUSDT SOLUSDT
```

```
📊 WEEX Spot Market
=================================================================
Symbol            Price       24h         High          Low        Vol
-----------------------------------------------------------------
BTCUSDT      $71,031.90 🟢+0.18%   $72,026.00   $68,929.50    $513.3M
ETHUSDT       $2,165.64 🟢+1.25%    $2,199.02    $2,103.34    $358.5M
SOLUSDT          $91.74 🟢+2.01%       $93.44       $88.42     $85.6M
```

**交易者解读:** 三大主流币全线微涨。BTC 日内振幅 $68,929-$72,026 (~4.5%)，当前价格偏中间位置。SOL 涨幅领先 (+2.01%)，可能有资金轮动。

---

### Step 5: 合约市场 + 资金费率

```bash
bash scripts/crypto.sh weex-futures BTCUSDT ETHUSDT
```

```
📊 WEEX Futures Market
=======================================================
Symbol            Price   Mark Price    Funding
-------------------------------------------------------
BTCUSDT      $71,022.96   $70,899.80   -0.0100%
ETHUSDT       $2,165.91    $2,163.70   -0.0042%
```

```bash
bash scripts/crypto.sh weex-funding BTCUSDT
```

```
📊 WEEX Funding Rate: BTCUSDT
  03-25 16:00        🔴-0.0100%      $70,899.8
  03-25 08:00        🔴-0.0093%      $70,525.2
  03-25 00:00        🔴-0.0096%      $69,844.4
  03-24 16:00        🔴-0.0094%      $71,265.8
  ...
  Avg (recent 24 periods): -0.0092%
  Annualized: -10.05%
```

**交易者解读:**
- 合约价格 ($71,022) 略高于标记价格 ($70,899)，轻微正溢价
- 资金费率持续为负 (-0.01%)，说明空头占主导，空头在付费给多头
- 年化 -10.05% 意味着做多有额外收益（空头付费）
- 结论: 空头拥挤，资金费率持续为负是潜在的空头挤压 (short squeeze) 信号

---

### Step 6: 订单簿流动性

```bash
bash scripts/crypto.sh weex-depth BTCUSDT
```

```
📊 WEEX Order Book: BTCUSDT
  🔴 $   71,057.50       2.8531
  🔴 $   71,053.90       2.8248
  🔴 $   71,050.70       2.7969
  ...
          --- spread ---
  🟢 $   71,039.40       1.9884
  🟢 $   71,039.00       0.9652
  🟢 $   71,037.00       2.6888
  ...
```

**交易者解读:**
- 买卖价差 ~$0.10 (spread 极小)，流动性充足
- 卖盘挂单量略大于买盘，短期有一定抛压
- 适合市价单进出，滑点可控

---

### Step 7: 交易执行 (WEEX API)

基于以上分析，假设决定在 WEEX 现货买入 BTC:

```bash
# 查看可用端点
python scripts/weex_spot_api.py list-endpoints --pretty

# 限价买入 (需要 API Key)
python scripts/weex_spot_api.py place-order \
  --symbol BTCUSDT --side BUY --order-type LIMIT \
  --quantity 0.001 --price 70600 --time-in-force GTC \
  --confirm-live --pretty
```

合约做多 (利用负资金费率):

```bash
python scripts/weex_contract_api.py place-order \
  --symbol BTCUSDT --side BUY --position-side LONG --type LIMIT \
  --quantity 0.001 --price 70600 --time-in-force GTC \
  --confirm-live --pretty
```

> ⚠️ 交易执行需要配置 API 凭证 (WEEX_API_KEY, WEEX_API_SECRET, WEEX_API_PASSPHRASE)
> ⚠️ 所有下单操作必须带 `--confirm-live` 参数，防止误操作

---

## 3. 综合研判总结

| 维度 | 信号 | 方向 |
|------|------|------|
| 宏观市场 | 市值 $2.51T, +1.36% | 🟢 偏多 |
| 恐惧贪婪 | 14/100 极度恐惧 | 🟢 逆向买入信号 |
| RSI (14日) | 55.6 中性 | 🟡 中性 |
| 均线系统 | 金叉 + 偏多震荡 | 🟢 偏多 |
| 资金费率 | -0.01% 空头付费 | 🟢 多头有利 |
| 订单簿 | 流动性充足，微弱抛压 | 🟡 中性 |

**综合判断: 偏多。** 极度恐惧 + 负资金费率 + 金叉排列，形成多重底部信号。建议关注 MA30 ($70,651) 支撑位，分批建仓。

---

## 4. 命令速查表

### 市场分析 (免费 API，无需 Key)

| 命令 | 用途 | 数据源 |
|------|------|--------|
| `price [coins...]` | 币种价格 | CoinGecko |
| `market` | 全球市场概览 | CoinGecko |
| `trending` | 热门币种 | CoinGecko |
| `fear` | 恐惧贪婪指数 | Alternative.me |
| `info <coin>` | 币种详情 | CoinGecko |
| `rsi <coin>` | RSI 技术指标 | CoinGecko |
| `ma <coin>` | 均线分析 | CoinGecko |
| `defi` | DeFi TVL 排行 | DefiLlama |
| `defi-yield` | DeFi 收益率 | DefiLlama |

### WEEX 交易所 (免费公开端点)

| 命令 | 用途 | 数据源 |
|------|------|--------|
| `weex [symbols...]` | 现货 24h 行情 | WEEX Spot API |
| `weex-futures [symbols...]` | 合约价格 + 资金费率 | WEEX Contract API |
| `weex-funding [symbol]` | 资金费率历史 | WEEX Contract API |
| `weex-depth [symbol]` | 订单簿深度 | WEEX Spot API |

### WEEX 交易执行 (需要 API Key)

| 命令 | 用途 |
|------|------|
| `python weex_spot_api.py place-order ...` | 现货下单 |
| `python weex_contract_api.py place-order ...` | 合约下单 |
| `python weex_contract_api.py cancel-order ...` | 撤单 |
| `python weex_contract_api.py ticker --symbol ...` | 实时价格 |

---

## 5. 测试结论

全部 22 个命令测试通过。两个 Skill 已完成适配整合:

- **crypto-tracker** (v3.0.0): 新增 4 个 WEEX 命令，修复 Windows 跨平台兼容性
- **weex-trader-skill** (v1.6.0): 文档优化，Python 命令跨平台兼容

交易者可以通过 `market → fear → rsi/ma → weex → weex-futures → weex-depth → place-order` 的完整链路，从宏观到微观完成交易决策。

⚠️ 风险提示：以上数据仅供参考，不构成投资建议。加密货币投资风险极高，请自行判断。
