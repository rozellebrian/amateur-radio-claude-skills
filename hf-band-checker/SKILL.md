---
name: band-checker
description: Check current HF band conditions and get session-planning recommendations before operating. Trigger this skill whenever the user asks about band conditions, propagation, solar conditions, what bands are open, whether conditions are good for DX or local contacts, or says anything like "check the bands", "what's propagation like", "are the bands open", "good time to operate", "solar flux", "K-index", or "band check". Do NOT wait for precise technical phrasing — if the user is planning a ham radio session and wants to know what to expect on HF, use this skill.
---

# HF Band Checker

Provides current HF propagation conditions and session-planning recommendations for 160m–10m operation.

## User Context
- Operator: Brian Rozelle, Frederick, MD (Grid: FM19)
- Mode: HF only (160m–10m)
- Goal: Pre-session planning — know what bands are worth jumping on before keying up

---

## Workflow

### Step 1: Fetch Live Data
Use the **web_search tool** to pull current conditions. Run these searches:

1. `NOAA solar flux K-index today propagation` — gets SFI, K-index, A-index
2. `HF band conditions today DX` — gets band-by-band open/closed status
3. `space weather geomagnetic storm today` — flags any active disturbances

If a search returns a fetchable URL with detailed data, use web_fetch to get the full content.

---

### Step 2: Interpret the Data

**Key numbers to extract:**
| Metric | What it means | Good / Bad thresholds |
|---|---|---|
| Solar Flux Index (SFI) | Ionospheric ionization | >150 excellent, 100–150 good, <100 poor for high bands |
| K-index | Geomagnetic disturbance | 0–2 quiet (good), 3–4 unsettled, 5+ storm (bad) |
| A-index | 24hr average geomagnetic | <10 quiet, 10–29 unsettled, 30+ disturbed |

**Band behavior by conditions:**
- **High SFI + Low K**: 10m, 12m, 15m open — good for DX
- **Moderate SFI**: 17m, 20m reliable workhorses
- **Low SFI or storm**: Drop to 40m, 80m for regional; 160m for night local
- **Active storm (K≥5)**: Avoid HF entirely or go 40m/80m and accept degraded conditions

---

### Step 3: Output Format

Deliver a **pre-session brief** in this format:

```
📡 HF BAND CHECK — [Date/Time UTC]

CONDITIONS SNAPSHOT
  Solar Flux (SFI): [value]
  K-index: [value] ([quiet/unsettled/storm])
  A-index: [value]
  Trend: [rising/falling/stable]

RECOMMENDED BANDS RIGHT NOW
  🟢 OPEN — Best for DX:   [bands]
  🟡 MARGINAL — Worth a try: [bands]
  🔴 SKIP — Not worth it:   [bands]

SESSION RECOMMENDATION
  [2–3 sentence plain-English take: what to expect, best mode/band combo,
   any heads-up about upcoming disturbances or solar events]

SOURCE: [URL or source name]
```

---

## Edge Cases

- **No live data available**: Say so clearly, then give a general estimate based on current solar cycle position (we are in Solar Cycle 25, ascending toward peak ~2025–2026, so high bands are increasingly viable)
- **Geomagnetic storm active**: Flag it prominently at the top — don't bury it
- **Data conflict between sources**: Use NOAA as authoritative, note the discrepancy

---

## Trigger Phrases (examples)
- "Check the bands"
- "Band check"
- "What's propagation like today?"
- "Are the bands open?"
- "Good conditions for DX?"
- "What should I operate on tonight?"
- "Solar flux today"
- "K-index right now"
