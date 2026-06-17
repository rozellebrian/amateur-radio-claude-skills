---
name: contest-calendar-checker
description: Check upcoming HF amateur radio contests in the next 30 days and provide the exchange format / rules for each. Trigger this skill whenever the user asks about contests, "what contests are coming up", "any contests this weekend", "contest calendar", "what's the exchange for X contest", or mentions a specific contest name and asks about format, rules, or scoring. Do NOT wait for the word "contest" explicitly — if the user is asking about competitive HF operating events, use this skill.
---

# HF Contest Calendar Checker

Identifies upcoming HF contests (SSB, digital, mixed-mode — not CW-only) and provides exchange formats so the user knows what to expect before operating.

## User Context
- Operator: Brian Rozelle, Frederick, MD (Grid: FM19), Extra class
- Mode: HF only, QRP-capable
- **Not interested in CW-only contests** — do not feature, highlight, or lead with these. See filtering rules below.
- Use case: Pre-session awareness — know what's running this weekend or this month, and what to send/log

---

## Workflow

### Step 1: Fetch Data — Use web_fetch Directly, Not web_search

web_search snippets for this topic return page metadata and junk, not usable contest listings. **Always use web_fetch on known URLs directly.** Use two sources, layered:

**Source A — ARRL Contest Calendar (primary, for 30-day / major-contest view)**
Fetch: `https://www.arrl.org/contest-calendar`
This gives the named major contests (Field Day, IARU HF Championship, Sweepstakes, DX contests, etc.) for the current month and several months out, in a clean static table. This is the most reliable source for big, well-known contests across a full 30-day window.

**Source B — WA7BNM 8-Day Calendar (secondary, for near-term granular detail)**
Fetch: `https://www.contestcalendar.com/weeklycont.php`
This gives full exchange-format detail (mode, bands, classes, exact exchange, scoring, log deadlines, rules links) but **only for an 8-day rolling window** — it does not reliably paginate forward. Do not rely on the "next"/"5-week"/"12-month" links on this site; they have returned stale or repeated data. Use this source only for what's happening in the next ~8 days, layered on top of Source A's longer view.

**Source C — ARRL Contest Corral PDFs (fills the mid-range gap)**
Source A (major contests) and Source B (8-day detail) leave a blind spot roughly between day 9 and day 30 — Source A only lists big named contests, and Source B can't see that far ahead. The Contest Corral is ARRL's monthly PDF that lists smaller/regional contests (including non-ARRL ones) day-by-day for an entire month, which closes that gap.

- The links live on the same ARRL contest-calendar page (Source A), under "Contest Corral - Includes Non-ARRL Contests," labeled by month and year, e.g.:
  `http://www.arrl.org/files/file/Contest%20Corral/2026/June%202026%20Corral.pdf`
- Always fetch **both the current month's and next month's** Corral PDF when the 30-day window spans a month boundary (which it almost always will).
- Use `web_fetch` directly on the PDF URL — don't search for it, pull it straight from the link structure on the ARRL page.
- Cross-check Corral entries against Source A/B to avoid listing the same contest twice; Corral entries are typically less detailed (date/name only, no exchange format), so when a contest also appears in Source B, prefer Source B's detail and just use Corral to confirm it's not missing anything.
- Still apply the CW-only exclusion rule to anything found here.

**If a small/weekly sprint isn't on any of the three sources**, it's not worth surfacing anyway given the filtering rule below.

### Step 2: Filter Results

Default window: **next 30 days from today's date**.

**Exclude entirely — do not list, do not mention as a footnote:**
- Any contest that is CW-only (mode listed as "CW" with no SSB/Digital/Mixed option)
- VHF/UHF-only contests, non-HF-band events

**Include:**
- SSB, Digital (FT8/FT4/RTTY), and Mixed-mode HF contests
- If a contest offers multiple modes including CW (e.g., "CW, SSB, Digital"), it's fine to include — just don't frame it around the CW portion

### Step 3: Output Format

```
📅 HF CONTEST CALENDAR — Next 30 Days
(as of [today's date])

[DATE/TIME RANGE in UTC]
  Contest: [Name]
  Mode: [SSB / Digital / Mixed — never lead with CW]
  Bands: [bands used]
  Class relevant to QRP: [Yes/No — note if QRP category exists]

  EXCHANGE FORMAT:
  [Sent]: [what you send]
  [Received]: [what you log from the other station]

  EXAMPLE EXCHANGE:
  "[realistic sample exchange in actual operating shorthand]"

  SCORING NOTE: [1 line — multiplier basis, points per QSO, etc.]
  RULES LINK: [URL if available]
  SOURCE: [ARRL / WA7BNM / ARRL Contest Corral]

---
[repeat per contest]
```

### Step 4: Prioritize
1. Major/well-known contests first (Field Day, IARU HF Championship, Sweepstakes, DX contests, etc.)
2. Contests with QRP categories highlighted with a 🔹 marker
3. Smaller/regional contests last
4. CW-only contests: omit entirely, don't even list as "skipped for CW" — just leave them out

---

## Exchange Format Reference (common contests — use as fallback if live data is incomplete)

| Contest | Typical Exchange |
|---|---|
| CQ WW DX (SSB) | RST + CQ Zone |
| ARRL DX (W/VE, Phone) | RS + State/Province (or power for DX stations working W/VE) |
| ARRL Sweepstakes (Phone) | Serial + Precedence + Call + Check + Section |
| CQ WPX (SSB) | RS + Serial Number |
| IARU HF Championship | RS(T) + ITU Zone (or HQ abbreviation for HQ stations) |
| NAQP (SSB) | Name + State/Province/Country |
| ARRL Field Day | Class + Section |
| 10-10 Contest | RS + 10-10 number + State |

Always verify against live fetched data — exchange formats occasionally change year to year.

---

## Edge Cases

- **No qualifying (non-CW) contests in next 30 days**: State this clearly, don't fabricate one
- **Contest format changed from prior year**: Flag it if fetched data shows a recent rule change
- **QRP-specific SSB/digital/mixed contest exists**: Always surface prominently since the user operates QRP
- **Ambiguous contest name** (e.g., "the DX contest"): Ask which one if multiple match
- **Gap between Source A and Source B's coverage** (days 9-30): This is now covered by Source C. If Source C is unreachable or returns nothing usable, say so plainly rather than implying nothing is happening in that window.
