# Amateur Radio Claude Skills

A collection of [Claude](https://claude.ai) skills for amateur radio operators — pre-built workflows that give Claude the context and instructions to handle ham radio-specific tasks consistently.

## Skills

### 📡 HF Band Checker

Checks current HF band conditions and gives a recommendation-style brief before an operating session.

**What it does:**
- Pulls live solar flux, K-index, and propagation data via web search (NOAA, RSGB Propagation News, and similar sources)
- Translates raw numbers into a plain-English read on which bands are worth trying
- Outputs open / marginal / skip bands for 160m–10m, oriented around DX potential

**Trigger phrases:** "check the bands," "band check," "what's propagation like today," "are the bands open," "good conditions for DX," "solar flux today"

**Best for:** Pre-session planning on HF, before keying up or calling CQ.

[`/hf-band-checker`](./hf-band-checker)

---

### 📅 Contest Calendar Checker

Surfaces upcoming HF contests for the next 30 days, with full exchange formats so there's no scrambling to find the rules mid-contest.

**What it does:**
- Pulls major contests (Field Day, IARU HF Championship, Sweepstakes, etc.) from the ARRL Contest Calendar
- Pulls granular near-term detail (exact exchange, scoring, log deadlines) from the WA7BNM 8-Day Calendar
- Fills the mid-range gap between those two using ARRL's monthly Contest Corral PDFs
- Filters out CW-only contests — SSB, digital, and mixed-mode only
- Flags QRP-friendly categories

**Trigger phrases:** "what contests are coming up," "any contests this weekend," "contest calendar," "what's the exchange for [contest]"

**Best for:** Weekend operating planning, knowing what to send before you're already in a pileup.

[`/hf-contest-calendar-checker`](./hf-contest-calendar-checker)

---

## Installation

Each skill folder contains a `SKILL.md` file. To use with Claude:

1. Download or clone this repo
2. Package the relevant skill folder as a `.skill` file (zip the folder contents)
3. Upload via Claude's skill upload interface, or place in your skills directory if self-hosting

## About

Built for HF/QRP operators who want Claude to handle the repetitive pre-session research — propagation checks, contest lookups — without re-explaining context every time.

Maintained by [Brian Rozelle](https://github.com/rozellebrian).
