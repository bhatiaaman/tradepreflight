# TradePreflight ✈️

> **Run your preflight before every trade.**

A multi-agent order intelligence system that validates trades against behavioral discipline, technical structure, candlestick patterns, support/resistance stations, and live options open interest — before the order is placed.

Built and designed by **[Aman Bhatia](https://github.com/amanbhatia)** as part of [Bhatiaverse](https://github.com/amanbhatia/bhatiaverse) — a personal trading terminal for Indian markets.

---

## The Problem

Most traders lose not because they lack a strategy, but because they break their own rules:

- Averaging into a losing position
- Placing orders against the prevailing trend
- Trading at overextended levels
- Adding to a sector already at max exposure
- Entering when VIX is spiking

A trading system should **stop you from placing that order**, not just warn you after the fact.

---

## The Solution — Five Agents, One Verdict

TradePreflight runs five specialized agents in parallel before every order. Each agent independently scores the trade. The scores are aggregated into a single verdict.

```
Order Submitted
      │
      ▼
┌─────────────────────────────────────────────┐
│              TradePreflight                 │
│                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │Behavioral│  │Structure │  │ Pattern  │  │
│  └──────────┘  └──────────┘  └──────────┘  │
│       ┌──────────┐  ┌──────────┐            │
│       │ Station  │  │    OI    │            │
│       └──────────┘  └──────────┘            │
│                                             │
│              Verdict Engine                 │
│     0 = Clear · <20 Caution · <45 Warn      │
│              ≥45 = DANGER                   │
└─────────────────────────────────────────────┘
      │
      ▼
  Proceed / Gate / Block
```

---

## The Five Agents

### 1. Behavioral Agent
*"Don't trade against yourself."*

Checks your positions and account state for discipline violations:

| Check | What it catches |
|---|---|
| Adding to loser | Averaging down on a position already in loss |
| Trend alignment | Bullish order when NIFTY is below EMA9 (or vice versa) |
| Position count | Too many open positions at once |
| VIX spike | Placing orders when India VIX is elevated |
| Duplicate order | Same symbol + direction already open |
| Sector exposure | Already overweight in this sector |

### 2. Structure Agent
*"Where are you entering, technically?"*

Analyses price structure using candles (15m, daily, weekly):

- EMA alignment (short/medium/long)
- VWAP position (intraday)
- Higher highs / lower lows structure
- Volume confirmation
- Swing vs MIS mode — different checks for each

### 3. Pattern Agent
*"What is the candle telling you?"*

Detects candlestick patterns on 15m and daily timeframes:

- Engulfing, Doji, Hammer, Shooting Star
- Pattern direction vs order direction conflict
- Recency weighting — patterns from last 2 candles score higher

### 4. Station Agent
*"Are you entering at a crowded station?"*

Identifies price "stations" — levels where significant accumulation/distribution happened:

- Volume-weighted support/resistance zones
- Entry too close to a strong resistance (for longs) or support (for shorts)
- Distance from station determines score severity

### 5. OI Agent
*"What are the big players doing?"*

Reads live options open interest for NIFTY/BANKNIFTY:

- Put wall vs Call wall positioning
- PCR (Put-Call Ratio) interpretation
- Max pain level proximity
- OI buildup direction vs order direction conflict

---

## Verdict Scale

| Score | Label | Action |
|---|---|---|
| 0 | Clear | All agents passed |
| 1–19 | Caution | Minor flags, proceed with awareness |
| 20–44 | Warning | Notable conflicts, review before placing |
| 45+ | Danger | Hard gate — significant risk detected |

Each agent contributes independently. A single agent hitting a hard check can push the verdict to Danger regardless of other scores.

---

## Direction Mapping

The system correctly maps order direction to market bias:

| Order | Interpretation |
|---|---|
| BUY CE | Bullish |
| SELL CE | Bearish |
| BUY PE | Bearish |
| SELL PE | Bullish |
| BUY/SELL EQ or FUT | Follows transaction type |

---

## Usage

```js
import { runPreflight } from 'tradepreflight';

const result = await runPreflight({
  symbol: 'NIFTY',
  instrumentType: 'CE',       // CE | PE | EQ | FUT
  transactionType: 'BUY',     // BUY | SELL
  quantity: 50,
  price: 24500,
  tradingMode: 'MIS',         // MIS | NRML (swing)

  // Market context
  kite,                       // KiteConnect instance
  positions: [...],           // current open positions
  vix: 14.5,
  niftySpot: 24480,
  niftyEMA9: 24350,
});

console.log(result.verdict);         // 'clear' | 'caution' | 'warning' | 'danger'
console.log(result.score);           // 0–100
console.log(result.agents);          // per-agent breakdown
console.log(result.behavioral);      // { checks: [...], verdict: 'caution' }
console.log(result.structure);       // { checks: [...], verdict: 'clear' }
```

---

## Architecture

```
tradepreflight/
├── index.js                  # Main entry — orchestrates all agents
├── agents/
│   ├── behavioral.js         # Discipline & account-state checks
│   ├── structure.js          # Technical structure (EMA, VWAP, HH/LL)
│   ├── pattern.js            # Candlestick pattern recognition
│   ├── station.js            # Support/resistance station proximity
│   └── oi.js                 # Live options open interest analysis
└── lib/
    ├── resolve-token.js      # Symbol → Kite instrument token (Redis cached)
    ├── sector-map.js         # Symbol → sector mapping (50 FnO stocks)
    └── stations.js           # Station detection algorithm
```

### Registry Pattern — Important

All agents use **paired `[fn, passLabel]` tuples** instead of `check.name` string lookups:

```js
const CHECKS = [
  [checkAddingToLoser, 'No loser averaging'],
  [checkTrendAlignment, 'Aligned with trend'],
  // ...
];

CHECKS.map(([check, passLabel]) => {
  const result = check(context);
  // result.passed → use passLabel directly
  // never use check.name — bundlers mangle function names in production
});
```

This is intentional. Next.js/SWC and most bundlers mangle function `.name` in production builds, breaking string-keyed lookups.

---

## Requirements

- Node.js 18+
- [Kite Connect](https://kite.trade/docs/connect/v3/) API credentials (Zerodha)
- Upstash Redis (for token map and candle caching) — optional but recommended

---

## Indian Market Focus

TradePreflight is purpose-built for Indian markets:

- NIFTY 50 and BANKNIFTY options (weekly + monthly expiry)
- FnO stock universe (50 stocks)
- India VIX integration
- Kite Connect API native
- IST-aware market hours detection

---

## Author

**Aman Bhatia**
- GitHub: [@amanbhatia](https://github.com/amanbhatia)
- Project: [Bhatiaverse](https://github.com/amanbhatia/bhatiaverse)

*TradePreflight was designed and built from scratch as the order intelligence layer of Bhatiaverse — a personal full-stack trading terminal. The multi-agent architecture, verdict engine, direction mapping, and registry pattern are original work.*

---

## License

Copyright (c) 2025 Aman Bhatia

Licensed under the [Apache License 2.0](LICENSE).

You are free to use, modify, and distribute this software. Attribution is required — keep the copyright notice intact.

---

## Contributing

PRs welcome. If you add a new agent or check, follow the existing `[fn, passLabel]` registry pattern and add it to the verdict engine in `index.js`.

---

*"Preflight isn't about doubting your strategy. It's about making sure you're flying the right plane, in the right direction, on the right day."*
