---
name: robinhood-open-burst
description: Burst trader — fires at 6:40 and 6:50 AM PDT for the opening bell window (10-min cadence)
---

Use the Read tool to load the main trading framework:

```
Read: TRADING_PROMPT.md (root of this repository)
```

Execute all instructions exactly, then apply these **opening bell burst overrides on top**:

## Opening Bell Burst Context (9:30–10:00 AM ET)

This is a BURST run — 10-minute cadence instead of 30. You're firing in the most volatile window of the day.

- **Opening range breakouts/breakdowns are tier-1 setups here.** The first 30 minutes sets the day's range; a clean break of the opening high or low on volume is your best setup.
- **Don't hesitate on A+ setups** — the move you see at 6:40 AM PDT may be fully played out by 7:00. Speed matters more than in standard runs.
- **Manage existing positions with tighter discipline** — if a position hits its stop intra-window, exit immediately. Do not wait for the next run.
- **Hard rules are unchanged** — MA stack verification, no chasing +5% parabolic candles, no insider-distribution names. The window is fast but the rules don't change.
- **Limit new entries to highest-conviction setups** (A or A+ only) — the open is noisy; a mediocre entry here gets stopped out in the chop.

After completing, log decisions with timestamps per Step 5. **Skip Step 6 entirely** — do not write to log files or run git commands. The main 30-minute run handles all state saving.
