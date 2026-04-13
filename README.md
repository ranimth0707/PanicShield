# 🛡️ PanicShield

**From Panic Sell to PanicShield.**

An AI agent skill that analyzes your portfolio and tells you **when to hold, partially exit, or fully exit** — based on data, not emotions.

Built for the [OKX Build X Hackathon](https://web3.okx.com/xlayer/build-x-hackathon) | Skills Arena

---

## Problem

Every crypto holder has experienced panic selling — dumping tokens at a loss because of fear, uncertainty, or herd mentality. On the flip side, many hold too long and watch profits evaporate.

The problem isn't a lack of willpower. The problem is **a lack of actionable, real-time intelligence** at the moment of decision.

**PanicShield** replaces emotional decision-making with data-driven exit strategies.

---

## What It Does

PanicShield is a **reusable agent skill** that any AI agent can install. Once installed, the agent gains the ability to:

1. **Full Portfolio Scan** — Analyze all tokens in a wallet and flag which ones need attention
2. **Single Token Check** — Deep-dive into a specific token's exit conditions
3. **Exit Verdict** — For each token, deliver a clear recommendation: 🟢 HOLD / 🟡 PARTIAL EXIT / 🔴 FULL EXIT — with reasoning and exit simulation

### Example Interaction

```
User: "Scan my portfolio, should I exit anything?"

PanicShield: 
📊 Portfolio Scan Complete — 5 tokens analyzed

🟢 HOLD: ETH ($2,100) 
   Liquidity strong, smart money accumulating, uptrend intact.

🟢 HOLD: USDC ($1,000)
   Stablecoin, no action needed.

🟡 PARTIAL EXIT: OKB ($800)
   Price 22% above 7-day average. 2 whale wallets sold this week.
   Suggestion: Take profit on 30-40%.
   Exit sim: 40% sell → ~$320 received, 0.2% price impact via OKX DEX.

🔴 FULL EXIT: Token Y ($400)
   Liquidity dropped 60% in 7 days. Top holder increased to 38%.
   1 smart money wallet fully exited. Security: new unverified contract change.
   Exit sim: Full sell → ~$365 received, 8.7% price impact via Uniswap.

🟢 HOLD: Token Z ($200)
   Fundamentals stable, no red flags.
```

---

## Architecture Overview

PanicShield chains **6 Onchain OS skills** in a sequential pipeline:

```
User Input ("scan portfolio" / "check token X")
        │
        ▼
┌─────────────────────────────┐
│  1. okx-wallet-portfolio    │  → Scan holdings, values, allocation
└──────────────┬──────────────┘
               ▼
┌─────────────────────────────┐
│  2. okx-dex-token           │  → Liquidity, holders, cluster analysis
└──────────────┬──────────────┘
               ▼
┌─────────────────────────────┐
│  3. okx-dex-market          │  → Price trend, K-line 7d, PnL
└──────────────┬──────────────┘
               ▼
┌─────────────────────────────┐
│  4. okx-security            │  → Contract risk, phishing, approvals
└──────────────┬──────────────┘
               ▼
┌─────────────────────────────┐
│  5. okx-dex-signal          │  → Whale/smart money movement
└──────────────┬──────────────┘
               ▼
┌─────────────────────────────┐
│  6. okx-dex-swap            │  → Simulate exit: quote, impact, route
└──────────────┬──────────────┘
               ▼
┌─────────────────────────────┐
│      Scoring Engine         │  → Weighted risk score per token
│                             │
│  Liquidity drop    → +2     │
│  Whale exiting     → +2     │
│  Price downtrend   → +1     │
│  Security flag     → +3     │
│  Holder conc. ↑    → +1     │
│                             │
│  0-2 = 🟢 HOLD             │
│  3-5 = 🟡 PARTIAL EXIT     │
│  6+  = 🔴 FULL EXIT        │
└──────────────┬──────────────┘
               ▼
        Final Verdict
   (per token with reasoning
    + exit simulation data)
```

---

## Onchain OS Skill Usage

| Skill | Purpose in PanicShield |
|-------|----------------------|
| `okx-wallet-portfolio` | Discover all token holdings, current values, and portfolio allocation percentages |
| `okx-dex-token` | Evaluate token health: liquidity depth, holder count, top holder concentration, holder cluster analysis, top traders activity |
| `okx-dex-market` | Analyze price trends: real-time price, 7-day K-line/candlestick data, index price comparison, wallet PnL |
| `okx-security` | Security assessment: token contract risk scan, DApp phishing detection, signature safety, approval management alerts |
| `okx-dex-signal` | Market intelligence: smart money/whale movement tracking, KOL signal tracking, detect accumulation or distribution patterns |
| `okx-dex-swap` | Exit simulation: get swap quotes without execution, calculate price impact, identify optimal routing across 500+ DEX sources |

---

## Working Mechanics

### Input Modes

**Mode 1: Full Portfolio Scan**
```
"Scan my portfolio"
"Check all my tokens"
"Any tokens I should exit?"
```
→ Scans all tokens in the connected Agentic Wallet, analyzes each, returns a prioritized list.

**Mode 2: Single Token Check**
```
"Should I exit Token X?"
"Check Token X exit conditions"
"How healthy is my Token X position?"
```
→ Deep analysis on one specific token with detailed breakdown.

### Scoring Logic

Each token receives a **PanicScore** (0-10) based on weighted signals:

| Signal | Weight | Source Skill | Trigger |
|--------|--------|-------------|---------|
| Liquidity drop >30% (7d) | +2 | okx-dex-token | Liquidity pools shrinking |
| Whale/smart money exiting | +2 | okx-dex-signal | Large holders selling |
| Price downtrend (7d) | +1 | okx-dex-market | Below 7-day moving average |
| Security flag detected | +3 | okx-security | Contract risk, phishing, suspicious approval |
| Holder concentration increasing | +1 | okx-dex-token | Top holder % growing (centralization risk) |
| Volume dying (24h vol <$50k) | +1 | okx-dex-token | Trading activity drying up |

**Verdict thresholds:**
- **PanicScore 0-2** → 🟢 **HOLD** — Fundamentals healthy, no red flags
- **PanicScore 3-5** → 🟡 **PARTIAL EXIT** — Some concerns, consider taking profit
- **PanicScore 6+** → 🔴 **FULL EXIT** — Multiple red flags, exit recommended

### Output Format

For each token, PanicShield returns:
1. **Verdict** — HOLD / PARTIAL EXIT / FULL EXIT
2. **PanicScore** — Numerical score with breakdown
3. **Key Signals** — Which factors triggered the score (with data)
4. **Exit Simulation** — If exit recommended: estimated amount received, price impact %, best route, DEX used
5. **Reasoning** — Human-readable explanation of why this verdict was given

---

## Deployment

### Agentic Wallet

- **Wallet Address:** `[TO BE ADDED AFTER DEPLOYMENT]`
- **Network:** X Layer
- **Role:** On-chain identity for PanicShield agent — used to query portfolio data, simulate swaps, and interact with X Layer ecosystem

### Installation

```bash
# Install Onchain OS skills
npx skills add okx/onchainos-skills

# Install PanicShield skill
npx skills add [repo-url]/panicshield-skill
```

---

## Positioning in X Layer Ecosystem

PanicShield fills a critical gap in the X Layer ecosystem: **risk management for token holders**.

While the ecosystem has tools for buying (DEX aggregation), discovering (token trending), and tracking (portfolio views) — there is no intelligent layer that helps users **decide when and how to exit positions safely**.

PanicShield serves as the **defensive layer** of the X Layer ecosystem:
- Protects users from emotional decision-making
- Reduces losses from holding dying tokens too long
- Increases trust in the ecosystem by promoting informed decisions
- Complements existing trading tools by adding exit intelligence

---

## Team

| Member | Role |
|--------|------|
| **Rani Mitha Riski** | Creator & Product Designer — Web3 KOL, content creator, and founder of Rektometer.click. Ambassador at Linera and OSL Indonesia. |

---

## Links

- **GitHub:** [this repo]
- **Demo Video:** [TO BE ADDED]
- **X Post:** [TO BE ADDED]
- **Moltbook:** [TO BE ADDED]

---

## License

MIT
