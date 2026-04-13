# PanicScore Rubric — Reference

## Signal Definitions

### LIQUIDITY_DROP (+2)
- **Trigger**: Liquidity pool depth dropped >30% over the past 7 days
- **Data source**: `onchainos token price-info` → `liquidity` field; compare to historical via `onchainos market kline`
- **Why it matters**: Liquidity drain precedes price collapse — insiders and LPs exit before retail

### WHALE_EXIT (+2)
- **Trigger**: Smart money / whale wallets show net selling on this token in last 24–48h
- **Data source**: `onchainos signal list` + `onchainos tracker activities --tracker-type smart_money`
- **Why it matters**: Smart money has better information — their exit often precedes retail panic

### PRICE_DOWNTREND (+1)
- **Trigger**: Current price is below the 7-day simple moving average
- **Data source**: `onchainos market kline --bar 1D --limit 7` → compute avg of close prices
- **Calculation**: `avg_price = sum(close[0..6]) / 7`; if `current_price < avg_price` → trigger
- **Why it matters**: Sustained downtrend = selling pressure not yet exhausted

### SECURITY_RISK (+3)
- **Trigger**: Any of the following:
  - `riskLevel = "high"` from token-scan
  - `isHoneypot = true`
  - Token tagged as `scam`, `rug`, `suspicious`, `phishing`
  - Contract unverified with high holder concentration
- **Data source**: `onchainos security token-scan`
- **Why it matters**: Contract-level risk = asymmetric downside. Weight is highest (+3) because this is not recoverable.

### HIGH_CONCENTRATION (+1)
- **Trigger**: Top 10 holders control >60% of token supply
- **Data source**: `onchainos token holders` → sum top 10 `holdingPercent`
- **Why it matters**: Concentrated supply = dump risk when large holders decide to exit

### LOW_VOLUME (+1)
- **Trigger**: 24-hour trading volume < $50,000 USD
- **Data source**: `onchainos token price-info` → `volume24h` field
- **Why it matters**: Low volume = illiquid market. Exit becomes costly due to slippage; also suggests loss of interest/momentum.

---

## Verdict Thresholds

| Score | Verdict         | Recommended Action |
|-------|-----------------|-------------------|
| 0–2   | 🟢 HOLD         | Monitor, no action needed |
| 3–5   | 🟡 PARTIAL EXIT | Reduce position by 50% to lock partial profits / cut risk |
| 6–10  | 🔴 FULL EXIT    | Exit immediately, simulate swap before executing |

---

## Score Calculation Example

Token XYZ on X Layer:
- Liquidity dropped 45% in 7d → **+2** (LIQUIDITY_DROP)
- Smart money: 2 buys, 5 sells → **+2** (WHALE_EXIT)
- Price: $0.08 vs 7d avg $0.11 → **+1** (PRICE_DOWNTREND)
- Security: riskLevel = "low" → **+0**
- Top 10 holders: 55% → **+0** (below 60% threshold)
- Volume 24h: $12,000 → **+1** (LOW_VOLUME)

**PanicScore = 6 → 🔴 FULL EXIT**

---

## Notes for Agent

- If a data source is unavailable (API error, chain not supported), **skip that signal and note it** in the output. Do NOT count unavailable signals as triggered.
- Maximum possible score is 10. Even if all signals fire, cap at 10.
- For tokens with `usdValue < $5`, still run analysis but note: "Low value position — cost of exit may exceed value."
