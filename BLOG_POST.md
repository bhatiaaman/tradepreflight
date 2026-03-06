# TradePreflight: Why I Built a Multi-Agent System to Stop Myself From Trading

*By Aman Bhatia | March 2025*

---

I've been an active trader in Indian markets for a while now. I trade NIFTY and BANKNIFTY options, some FnO stocks. I have a strategy. I know my setups. And yet — I kept placing orders I knew I shouldn't.

Not because I didn't know better. Because in the moment, the emotions override the knowledge.

That's the problem TradePreflight solves.

---

## The Gap Between Decision and Execution

There's a moment between "I want to place this order" and "the order is placed" where most trading accounts bleed.

In that gap, you're rationalizing. You're telling yourself the loss will recover. You're telling yourself this setup is different. You're telling yourself VIX at 22 is fine, your conviction is strong.

A trading strategy tells you *what* to trade. Discipline tells you *when not to*. And discipline is the thing that breaks down under pressure.

I needed something systematic in that gap. Not another rule I'd remember sometimes. Something that *runs automatically* and *forces me to see what I'm doing* before the order goes through.

---

## The Preflight Analogy

Commercial pilots don't trust their gut before takeoff — no matter how experienced they are. They run a preflight checklist. Every time. Every flight.

It's not that they don't know how to fly. It's that the checklist catches the things that experience makes you complacent about.

Traders need the same thing. A systematic, non-negotiable check that runs before every order.

That's the mental model behind TradePreflight.

---

## The Architecture: Five Agents, One Verdict

I designed TradePreflight as a multi-agent system. Each agent is specialized. Each scores independently. The scores roll up into one verdict.

### Agent 1: Behavioral
This is the most important agent, and it needs no candles.

It checks the state of your account and positions against a set of discipline rules:

- **Adding to a loser** — are you averaging into a position already in loss?
- **Trend alignment** — is your order direction aligned with NIFTY's EMA trend?
- **Position count** — are you already stretched across too many open positions?
- **VIX** — is India VIX elevated? Elevated volatility = elevated risk per contract.
- **Duplicate order** — do you already have this exact position open?
- **Sector exposure** — are you already overweight in this sector?

These are pure behavioral checks. They don't care about your entry price or your technical setup. They catch the things discipline is supposed to catch.

### Agent 2: Structure
The Structure agent looks at price structure using candles across 15-minute, daily, and weekly timeframes:

- EMA alignment (short, medium, long)
- VWAP position for intraday trades
- Higher highs / lower lows trend structure
- Volume confirmation

It distinguishes between MIS (intraday) and NRML (swing) mode — the checks that matter are different depending on your trade duration.

### Agent 3: Pattern
The Pattern agent detects candlestick formations and checks whether they support or contradict your intended direction:

- Engulfing candles
- Doji (indecision)
- Hammer and Shooting Star
- Pin bars

Patterns in the last 1–2 candles score higher than older patterns. A fresh shooting star at a resistance when you're trying to go long is a different signal than one from 10 candles ago.

### Agent 4: Station
The Station agent identifies "stations" — price levels where significant volume accumulation or distribution has occurred. Think of them as crowded platforms where buyers or sellers are anchored.

It checks:
- Is your entry too close to a strong resistance (if going long)?
- Is your entry too close to a strong support (if going short)?
- How far are you from the nearest station?

Entering right at a station dramatically increases the probability of a reversal against your trade.

### Agent 5: OI (Options Open Interest)
The OI agent is specific to NIFTY and BANKNIFTY. It reads live options open interest from the full option chain and interprets:

- Put wall vs Call wall positioning
- PCR (Put-Call Ratio) — above 1.2 is bullish, below 0.8 is bearish
- Max pain proximity
- OI buildup direction (Long Buildup, Short Covering, Short Buildup, Long Unwinding)

If you're trying to go bullish but OI shows heavy call writing at current strikes, you want to know that before placing.

---

## The Verdict Engine

Each agent produces a score and a per-check breakdown. The verdict engine aggregates:

| Score | Verdict | Action |
|---|---|---|
| 0 | Clear | All agents passed — proceed |
| 1–19 | Caution | Minor flags — proceed with awareness |
| 20–44 | Warning | Notable conflicts — review before placing |
| 45+ | Danger | Hard gate — significant risk detected |

A single hard-failing check can push the verdict to Danger regardless of other scores. This is intentional — some rules are non-negotiable.

You can still override a Danger verdict. But you have to do it consciously, with the specific warnings visible on screen. That's the point — not to stop you trading, but to stop you trading unconsciously.

---

## One Design Decision I'm Proud Of: Direction Mapping

Options orders have counterintuitive direction mapping. Most systems get this wrong.

- **BUY CE** = Bullish (correct)
- **SELL CE** = Bearish ← systems that only look at BUY/SELL miss this
- **BUY PE** = Bearish ← and this
- **SELL PE** = Bullish

If you map direction wrong, every single check in the system is backwards. The Behavioral agent would flag a perfectly sensible SELL PE as "going against the bullish trend" when it's actually a bullish trade.

Getting this right means the system validates your *intent* — are you trying to profit from a move up or down — not just the transaction type.

---

## The Registry Pattern

One technical decision worth documenting: all agents use a **paired `[fn, passLabel]` registry** instead of function name lookups.

```js
const CHECKS = [
  [checkAddingToLoser,   'No loser averaging'],
  [checkTrendAlignment,  'Aligned with trend'],
  [checkPositionCount,   'Position count OK'],
];

CHECKS.map(([check, passLabel]) => {
  const result = check(context);
  // use passLabel directly — never check.name
});
```

Why? Next.js's SWC bundler (and most production bundlers) mangle function `.name` in build output. A lookup like `PASS_LABELS[check.name]` works in development and silently fails in production, showing blank labels everywhere. The paired tuple approach is immune to bundler behavior.

This cost me a debugging session before I figured it out. Documenting it here so it saves someone else the same session.

---

## What's Next

TradePreflight is the intelligence layer. The terminal it runs inside — Bhatiaverse — also has:

- Live option chain with PCR and max pain
- Market commentary with bias history tracking
- Pre-market plan generation (AI + template modes)
- FnO movers, sector rotation signals
- Full order placement with multi-tier warning model

I'm open-sourcing TradePreflight as a standalone package first, with the goal of making it easy to plug into any Kite Connect-based terminal.

---

## Try It

GitHub: [github.com/amanbhatia/tradepreflight](https://github.com/amanbhatia/tradepreflight)

Licensed under Apache 2.0 — use it freely, build on it, just keep the attribution.

If you're building something on Kite Connect, or you have ideas for new checks, open an issue or PR. The system is designed to be extended — each new agent is just a new file in the `agents/` directory.

---

*Aman Bhatia builds Bhatiaverse — a personal full-stack trading terminal for Indian markets.*
*Twitter/X: @amanbhatia*

---

> "Preflight isn't about doubting your strategy. It's about making sure you're flying the right plane, in the right direction, on the right day."
