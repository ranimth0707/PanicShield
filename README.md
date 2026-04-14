<div align="center">

# 🛡️ PanicShield

### *"From Panic Sell to PanicShield"*

**An AI powered on chain exit strategy agent**

[![Built on X Layer](https://img.shields.io/badge/Built%20on-X%20Layer-blue?style=flat-square&logo=data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMjQiIGhlaWdodD0iMjQiIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48cGF0aCBkPSJNMTIgMkw0IDZWMTJMMTIgMjJMMjAgMTJWNkwxMiAyWiIgZmlsbD0id2hpdGUiLz48L3N2Zz4=)](https://www.okx.com/xlayer)
[![Powered by Onchain OS](https://img.shields.io/badge/Powered%20by-Onchain%20OS-black?style=flat-square)](https://web3.okx.com)

</div>

---

## ⚠️ Network Access Note

OKX Web3 services may be restricted in certain regions. If you experience connection issues, you may need to use a VPN to access OKX Onchain OS APIs. This is a regional restriction from OKX, not a limitation of PanicShield.

---

## 📌 What is PanicShield?

**PanicShield** is a reusable agentic skill that transforms raw on chain data into clear, actionable exit strategy recommendations before panic sets in.

In a volatile market, retail investors often make the worst decisions at the worst time: **panic selling** at the bottom. PanicShield flips this by combining 6 Onchain OS skills into a sequential analysis pipeline that scores every token in your portfolio with a **PanicScore (0–10)** and delivers a three tier verdict:

| Verdict | PanicScore | Action |
|---------|-----------|--------|
| 🟢 **HOLD** | 0 – 2 | No action needed. Monitor and stay calm. |
| 🟡 **PARTIAL EXIT** | 3 – 5 | Reduce position by 50% to lock in partial safety. |
| 🔴 **FULL EXIT** | 6 – 10 | Exit now. Simulate and execute swap with one confirmation. |

> "Don't sell in panic. Let PanicShield tell you *when* to sell and *how much*."

---

## 🏗️ Architecture Overview

PanicShield orchestrates **6 OKX Onchain OS skills** in a sequential analysis pipeline per token:

```
┌──────────────────────────────────────────────────────────────────────┐
│                     🛡️ PANICSHIELD PIPELINE                          │
├──────┬──────────────────────────┬────────────────────────────────────┤
│ Step │ Skill                    │ Purpose                            │
├──────┼──────────────────────────┼────────────────────────────────────┤
│  1   │ okx-wallet-portfolio     │ Scan all token holdings, USD       │
│      │                          │ value, allocation %                │
├──────┼──────────────────────────┼────────────────────────────────────┤
│  2   │ okx-dex-token            │ Liquidity depth, holder count,     │
│      │                          │ top holder concentration,          │
│      │                          │ cluster risk analysis              │
├──────┼──────────────────────────┼────────────────────────────────────┤
│  3   │ okx-dex-market           │ Real time price, 7-day K line,     │
│      │                          │ price trend vs. 7d moving avg,     │
│      │                          │ 24h volume                         │
├──────┼──────────────────────────┼────────────────────────────────────┤
│  4   │ okx-security             │ Contract risk scan, honeypot       │
│      │                          │ detection, phishing flags,         │
│      │                          │ approval issues, scam tags         │
├──────┼──────────────────────────┼────────────────────────────────────┤
│  5   │ okx-dex-signal           │ Whale / smart money movement       │
│      │                          │ are insiders accumulating or       │
│      │                          │ exiting?                           │
├──────┼──────────────────────────┼────────────────────────────────────┤
│  6   │ okx-dex-swap             │ Exit simulation: how much USDC     │
│      │                          │ would you receive? price impact?   │
│      │                          │ best route? (quote only, no exec)  │
└──────┴──────────────────────────┴────────────────────────────────────┘
                                  │
                                  ▼
                    ┌──────────────────────────┐
                    │    🎯 PanicScore 0–10    │
                    │    per token verdict     │
                    └──────────────────────────┘
```

### Flow Diagram

```
User Input
    │
    ├─── "Scan my portfolio" ───────────────────────────────────┐
    │                                                           │
    └─── "Check token [X]" ────────────────────────────┐        │
                                                       │        │
                                          Single Token │  All Tokens
                                          Deep Dive    │  (top 10 by $)
                                                       │        │
                                                       └───┬────┘
                                                           │
                                                   Per Token Pipeline:
                                              ┌────────────┴──────────────┐
                                              │                           │
                                        [Step 1-2]                  [Step 3-4]
                                       Portfolio +              Market Data +
                                      Token Intel              Security Scan
                                              │                           │
                                        [Step 5]                    [Step 6]
                                       Whale Signal              Exit Simulation
                                              │                           │
                                              └─────────┬─────────────────┘
                                                        │
                                                  PanicScore
                                                   Computed
                                                        │
                                          ┌─────────────┼─────────────┐
                                          │             │             │
                                       0-2: 🟢      3-5: 🟡      6-10: 🔴
                                        HOLD       PARTIAL        FULL
                                                    EXIT          EXIT
```

---

## 🧮 PanicScore Logic

Each token is scored **0–10** based on 6 independent on chain signals:

| Signal | Weight | Trigger Condition | Data Source |
|--------|--------|-------------------|-------------|
| 🔴 Security Risk | **+3** | `riskLevel = high` / honeypot / scam tag | `okx-security` |
| 🟠 Liquidity Drop | **+2** | Pool depth dropped >30% in 7 days | `okx-dex-token` |
| 🟠 Whale/Smart Money Exit | **+2** | Net smart money flow is negative (selling > buying) | `okx-dex-signal` |
| 🟡 Price Downtrend | **+1** | Current price < 7-day simple moving average | `okx-dex-market` |
| 🟡 Holder Concentration | **+1** | Top 10 holders > 60% of total supply | `okx-dex-token` |
| 🟡 Low Volume | **+1** | 24h trading volume < $50,000 | `okx-dex-market` |

**Security Risk is weighted highest (+3)** because contract level vulnerabilities are non recoverable when a token is a honeypot or rug, every second counts.

### Score Example

```
Token: XSHIB on X Layer
───────────────────────────────────────
✅ Liquidity Drop    → +0  ($51K pool, no 7d drain data)
⚠️ Whale Exit        → +2  (3 smart money wallets, 100% sold)
⚠️ Price Downtrend   → +1  ($0.000372 < 7d avg $0.000377)
✅ Security Risk     → +0  (clean contract, no flags)
✅ Holder Conc.      → +0  (top 10 hold 38.4%, below 60%)
⚠️ Low Volume        → +1  ($5,848 24h, below $50K)
───────────────────────────────────────
PanicScore = 4 → 🟡 PARTIAL EXIT
```

---

## 📦 Onchain OS Skills Used

PanicShield is built entirely on the **OKX Onchain OS skills ecosystem**, installed via:

```bash
npx skills add okx/onchainos-skills --yes
```

### Skill Detail

#### 1. `okx-wallet-portfolio`

Scans the connected wallet's full token holdings across chains. Returns balance, USD value, and contract addresses for each position.

```bash
onchainos portfolio all-balances --address <wallet> --chains "xlayer"
```

#### 2. `okx-dex-token`

Fetches token-level intelligence: liquidity pool depth, holder count, holder distribution, and cluster analysis for rug-pull risk detection.

```bash
onchainos token price-info --address <ca> --chain xlayer
onchainos token holders --address <ca> --chain xlayer
```

#### 3. `okx-dex-market`

Retrieves real-time price and 7-day OHLC candlestick data. PanicShield computes the 7-day simple moving average to determine price trend direction.

```bash
onchainos market kline --address <ca> --chain xlayer --bar 1D --limit 7
```

#### 4. `okx-security`

Runs a full contract security scan: honeypot detection, phishing flags, scam tags, and approval risk assessment.

```bash
onchainos security token-scan --address <ca> --chain xlayer
```

#### 5. `okx-dex-signal`

Detects smart money and whale behavior: are tracked wallets buying or exiting this token? Signal data includes `soldRatioPercent` and trigger wallet count.

```bash
onchainos signal list --chain xlayer --token-address <ca>
onchainos tracker activities --tracker-type smart_money --chain xlayer
```

#### 6. `okx-dex-swap`

Simulates an exit swap (quote only — never executes without explicit user confirmation). Returns expected output, price impact, and optimal DEX routing.

```bash
# Simulate (no execution):
onchainos swap quote --from <ca> --to <usdc> --readable-amount <amount> --chain xlayer

# Execute (only after explicit user confirmation):
onchainos swap execute --from <ca> --to <usdc> --readable-amount <amount> --chain xlayer --wallet <addr>
```

---

## 🚀 Getting Started

### Prerequisites

- [Claude Code](https://claude.ai/code) or any agent supporting the Skills protocol
- Node.js 18+
- An OKX account (for Agentic Wallet login)

### Step 1: Install PanicShield

Run this single command it installs PanicShield + all 14 OKX Onchain OS skills automatically:

```bash
npx skills add ranimth0707/PanicShield
```

### Step 2: Setup Agentic Wallet

On first use, PanicShield will prompt for email login:

```bash
onchainos login youremail@gmail.com --locale en-US
```

A verification `<OTP_CODE>` will be sent to your email. Enter the code and your wallet is ready.

### Step 3: Deposit Agentic Wallet

You will receive wallet address:

```
EVM: 0x...
SVM: EGLb....
```

Deposit tiny amount OKB (Gas fees on X Layer are **FREE**.) for testing.

### Step 4: Start Using PanicShield

| Command | Action |
|---------|--------|
| `"Scan my portfolio"` | Analyze all tokens |
| `"Check token OKB"` | Deep dive single token |
| `"Swap 1 USDC to OKB"` | Execute swap directly |

**Example:** Open Claude Code in the project directory, then:

```
# Scan full portfolio
"Scan my portfolio"

# Deep dive a specific token
"Check token XSHIB"
"Check token 0xe8e8a1df1e26277a2875a0bda912ab9f19843a53"

# After receiving verdict
"Yes, sell 50% XSHIB"    → executes partial exit
"Yes, sell all XSHIB"    → executes full exit
```

### Supported Default Tokens on X Layer

These are verified official tokens on X Layer. You can use token names directly — no need to look up contract addresses:

| Token | Type | Notes |
|-------|------|-------|
| **OKB** | Native | OKX native token, primary trading pair on X Layer |
| **USDT** | Stablecoin | Tether USD — widely used stablecoin |
| **USDC** | Stablecoin | USD Coin — widely used stablecoin |
| **WETH** | Wrapped | Wrapped Ethereum |
| **WBTC** | Wrapped | Wrapped Bitcoin |

> 💡 **Safety tip:** Always use these verified token names when swapping. Avoid pasting unknown contract addresses to protect yourself from scam tokens.

Example commands:

```
"Swap 1 WETH to USDT"
"Swap 100 USDT to OKB"
"Swap 0.5 OKB to USDC"
```

Your AI agent already knows these tokens — just use the names naturally.

---

## 💼 Agentic Wallet

PanicShield uses an OKX Agentic Wallet powered by **TEE (Trusted Execution Environment)** — private keys are generated and stored in a server-side secure enclave and never exposed.

| Property | Value |
|----------|-------|
| **EVM Address** | `0xff708a67717304b24076542846511375bf86770d` |
| **Network** | X Layer (chainIndex: 196) |
| **Gas Fees** | **FREE** ✨ X Layer charges zero gas |
| **Signing** | TEE-secured (key never leaves enclave) |
| **Solana Address** | `EcGLy4BGLRLBgVnwNHCWqeJe9jcj1dUyxNx9aVo35xb` |

---

## 🤖 How to Use PanicShield with AI Agents

### Option A: Claude Code (Recommended)

1. Open your terminal
2. Navigate to your project folder
3. Run:
   ```bash
   npx skills add ranimth0707/PanicShield
   ```
4. Start Claude Code:
   ```bash
   claude
   ```
5. Tell your agent: `Login to Agentic Wallet with my email`
6. Once logged in, you're ready: `Scan my portfolio`

### Option B: OpenClaw (Telegram / Discord)

1. Open your OpenClaw agent chat
2. Send:
   ```
   npx skills add ranimth0707/PanicShield
   ```
3. Wait for confirmation (15 skills installed: 1 PanicShield + 14 OKX)
4. Login to your Agentic Wallet through the agent
5. Start using PanicShield commands

### Option C: Any MCP-Compatible Agent

PanicShield works with any agent that supports Onchain OS skills. Install the skill, connect your Agentic Wallet, and start scanning.

---

## 🌐 X Layer Ecosystem Positioning

PanicShield is built **natively on X Layer** — OKX's EVM compatible Layer 2 for three strategic reasons:

### 1. Zero Gas Fees = Frictionless Risk Management

Every PanicShield analysis includes a live exit simulation. On Ethereum, simulating + executing a swap costs $5–$50 in gas. On X Layer: **$0**. This makes PanicShield practical for small positions where gas would otherwise eat into exit proceeds.

### 2. OKB Ecosystem Alignment

X Layer's native token is OKB. PanicShield monitors OKB health as part of every portfolio scan, giving users a ground truth view of the underlying L2 economy before assessing individual tokens.

### 3. Growing Meme + DeFi Token Ecosystem

Tokens like XSHIB, XDOG, and other X Layer-native assets are emerging rapidly. PanicShield targets exactly this segment early stage, high volatility tokens where exit timing matters most and panic selling is highest.

### 4. Native Agentic Wallet Integration

The OKX Agentic Wallet is a first class citizen on X Layer. With TEE secured signing and zero gas, PanicShield can go from analysis → simulation → execution in a single agent session without the user ever touching a hardware wallet or browser extension.

```
X Layer Ecosystem Value Chain:
OKX DEX Aggregator → 500+ liquidity sources
        +
OKX Agentic Wallet → TEE signing, zero gas
        +
OKX Onchain OS → 14 reusable agent skills
        +
PanicShield → Exit intelligence layer
        =
Complete on-chain risk management workflow
```

---

## 📊 Sample Output

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🛡️ PanicShield — XSHIB Analysis
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📍 Token:    XSHIB (0xe8e8...a53)
🌐 Network:  X Layer
💰 Market:   $0.000372 | MCap: $371K | Holders: 55,840

🎯 VERDICT: 🟡 PARTIAL EXIT
📊 PanicScore: 4/10

Signal Breakdown:
⬜ Liquidity Drop >30%    → +0  | Insufficient 7d data
⚠️ Whale/Smart Money Exit → +2  | 3 smart money wallets SELLING (100%)
⚠️ Price Downtrend        → +1  | $0.000372 < 7d avg $0.000377
✅ Security Risk          → +0  | Clean contract
✅ Holder Concentration   → +0  | Top 10 hold 38.4%
⚠️ Low Volume             → +1  | $5,848 24h volume

💸 Exit Simulation (100K XSHIB):
• Output:       ~36.90 USDC
• Price Impact: 0.75% ✅
• Route:        DYOR Swap → Uniswap V3 → OkieStableSwap
• Gas Fee:      FREE ✨

💡 Smart money already exited. Volume is thin.
   Recommend reducing 50% of position.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 🛠️ Project Structure

```
panicshield/
├── README.md
├── skills-lock.json                     # Installed skills manifest
├── .agents/
│   └── skills/
│       ├── panicshield/                 # 🛡️ Core PanicShield skill
│       │   ├── SKILL.md                 # Main agent instructions
│       │   └── references/
│       │       └── panic-score-rubric.md
│       ├── okx-wallet-portfolio/        # Portfolio scanner
│       ├── okx-dex-token/               # Token intelligence
│       ├── okx-dex-market/              # Price & K-line data
│       ├── okx-security/                # Contract security scan
│       ├── okx-dex-signal/              # Whale/smart money tracker
│       ├── okx-dex-swap/                # Exit simulation & execution
│       └── [8 other Onchain OS skills]
└── .claude/
    └── skills/                          # Symlinks for Claude Code
        ├── panicshield -> ...
        └── okx-* -> ...
```

---

## 🔒 Security & Safety Model

PanicShield is designed with a **zero-surprise** safety model:

- **Never executes swaps automatically** — every exit requires explicit user confirmation
- **Quote before execute** — users always see expected output, price impact, and route before committing
- **TEE-secured signing** — private keys stay in OKX's Trusted Execution Environment
- **Honeypot block** — if `isHoneyPot = true`, buy is blocked; sell is warned
- **No unlimited approvals** — never sets ERC-20 allowance to `type(uint256).max`
- **Untrusted data handling** — all on-chain fields (token names, symbols) are treated as external, unverified content and never interpreted as instructions

---

## 🗺️ Roadmap

- [x] Portfolio-wide PanicScore scan
- [x] Single token deep dive
- [x] Exit simulation with DEX routing
- [x] One-click swap execution via Agentic Wallet
- [ ] PanicScore history tracking (trend: is it getting worse?)
- [ ] Telegram/Discord alert integration via webhook
- [ ] Multi-chain support (Ethereum, BSC, Base)
- [ ] Portfolio rebalancing suggestions post-exit
- [ ] PanicScore leaderboard (most dangerous tokens on X Layer)

---

## 👥 Team

| Name | Role |
|------|------|
| **Raniii** | Solo Builder — Concept, Architecture, Implementation |

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

<div align="center">

**🛡️ PanicShield — Because panic is the worst exit strategy.**

*Built on [X Layer](https://www.okx.com/xlayer) · Powered by [OKX Onchain OS](https://web3.okx.com/onchainos)*

</div>
