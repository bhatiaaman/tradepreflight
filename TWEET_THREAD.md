# TradePreflight — Tweet Thread

> Copy-paste ready. Post as a thread. Tag #trading #zerodha #buildinpublic #indiatech

---

**Tweet 1 (Hook)**
Most traders have a strategy.
Most traders still lose.

Not because the strategy is wrong — because they break their own rules the moment emotions kick in.

I built TradePreflight to fix that. 🧵

---

**Tweet 2 (The Problem)**
Every trader knows these moments:

→ "This loss will recover, let me average"
→ "Market is bearish but this one feels different"
→ "VIX is at 22 but it's fine, my setup is strong"
→ "I already have 3 open positions, one more won't hurt"

These aren't strategy failures. They're discipline failures.

---

**Tweet 3 (The Insight)**
Pilots don't trust their gut before takeoff.

They run a preflight checklist — every time, no exceptions, no matter how experienced they are.

Traders need the same thing. A systematic check that runs BEFORE the order goes through.

That's what I built.

---

**Tweet 4 (The System)**
TradePreflight runs 5 agents in parallel before every order:

1. 🧠 Behavioral — are you breaking discipline?
2. 📐 Structure — are you entering in a technically sound zone?
3. 🕯️ Pattern — what is the candle actually saying?
4. 🚉 Station — are you entering at a crowded support/resistance?
5. 📊 OI — what are the big players doing in options?

---

**Tweet 5 (Behavioral Agent)**
The Behavioral agent is the most important.

It checks:
✗ Are you averaging into a loser?
✗ Is your order direction against the NIFTY trend?
✗ Do you already have this position open?
✗ Is your sector exposure already maxed?
✗ Is VIX spiking?

No candles needed. Pure discipline enforcement.

---

**Tweet 6 (The Verdict)**
Each agent scores independently.

The scores roll up into one verdict:

🟢 Clear (0) — all good, proceed
🟡 Caution (1–19) — minor flags
🟠 Warning (20–44) — review before placing
🔴 Danger (45+) — hard gate, don't place this

You can still override. But you have to consciously choose to.

---

**Tweet 7 (Direction Mapping)**
One thing I spent time getting right — direction mapping.

BUY CE = Bullish
SELL CE = Bearish ← most systems miss this
BUY PE = Bearish
SELL PE = Bullish ← and this

Wrong mapping = every check is backwards. The system validates the *intent*, not just the transaction type.

---

**Tweet 8 (Tech)**
Built on:
→ Next.js App Router
→ Kite Connect (Zerodha) API
→ Upstash Redis for candle + token caching
→ 5 parallel agents, one verdict engine

Open sourced under Apache 2.0 — you can use it, modify it, ship it. Just keep the attribution.

---

**Tweet 9 (CTA)**
It's called TradePreflight.

GitHub: github.com/amanbhatia/tradepreflight

If you're building on Kite Connect or want to add this to your own terminal — it's designed to be plugged in.

⭐ Star it if you find it useful. Feedback welcome.

#trading #zerodha #kiteconnect #buildinpublic #indiatech #nifty #algotrading

---

**Tweet 10 (Personal)**
I built this because I needed it for myself.

I kept placing orders I knew I shouldn't. My strategy was fine. My execution wasn't.

TradePreflight is what sits between the decision and the order. That gap is where most trading accounts bleed.

Built by @amanbhatia | Part of Bhatiaverse 🌐
