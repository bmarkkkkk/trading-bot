# Performance Log — Closed Trade Record

Append-only record of every closed trade. The agent reads this periodically to learn what's actually working (which directions, setups, sectors win) and adapts. Never delete rows — this is the edge-discovery dataset.

## Format (one row per closed trade):
| Date | Ticker | Dir (call/put/eq) | Setup | Entry | Exit | P&L $ | P&L % | Hold | Win? | Note |
|------|--------|-------------------|-------|-------|------|-------|-------|------|------|------|

## Running tally (agent updates periodically):
- Total closed trades: 0
- Win rate: —
- Calls win rate: — | Puts win rate: — | Equity win rate: —
- Best setup type: — | Worst setup type: —
- Avg winner: — | Avg loser: —

---

## TRADES

| Date | Ticker | Dir | Setup | Entry | Exit | P&L $ | P&L % | Hold | Win? | Note |
|------|--------|-----|-------|-------|------|-------|-------|------|------|------|
