---
name: robinhood-close-burst
description: Burst trader — fires at 12:40 and 12:50 PM PDT for the closing bell window (10-min cadence)
---

Use the Read tool to load the main trading framework:

```
Read: TRADING_PROMPT.md (root of this repository)
```

Execute all instructions exactly, then apply these **closing bell burst overrides on top**:

## Closing Bell Burst Context (3:30–4:00 PM ET)

This is a BURST run — 10-minute cadence instead of 30. You're in the last 30 minutes of the trading day.

- **Overnight hold decision is critical.** For every open position, explicitly decide: hold overnight or close? Default lean is to close — small accounts should not carry unnecessary overnight gap risk. Hold only if: (a) the thesis is strong and momentum is intact into the close, AND (b) you've taken partial profits to reduce cost basis (house money).
- **End-of-day institutional flows.** Watch for sudden volume spikes — large funds entering or exiting for rebalancing. If your position fades hard into the close on rising volume, get out.
- **Late-day breakouts are valid setups.** Names that chop sideways midday and break out in the last 30 min with volume often continue into the next day.
- **Tighter stops than midday** — with 30 minutes left, there's no time to recover from a bad entry. Only take setups with very clear risk levels.
- **Hard rules unchanged** — MA stack, no chasing, liquidity gates on options.

**Overnight disposition rules (apply in Step 2 position review):**
- Position is profitable + momentum intact → consider holding, but sell at least 1 contract / half shares to lock in cost basis
- Position is flat or slightly down → close it. Don't give the overnight gap a chance to turn a flat into a loss.
- Position is a winner showing signs of fading volume into close → take full profit now. Don't let a winner become a loser overnight.

After completing, log decisions with timestamps per Step 5. **Skip Step 6 entirely** — do not write to log files or run git commands. The main 30-minute run handles all state saving.
