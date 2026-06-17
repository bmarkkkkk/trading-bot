# Trade Journal
Append-only log. Each run adds one entry at the bottom.

---
## Format

**[DATE] [TIME ET] — Run Log**
- Portfolio: $[value] | Buying power: $[bp]
- Positions: [list with sizes]
- Actions: [BUY/SELL/HOLD/SKIP — instrument — qty — price — reason]
- Closed P&L: [if any trades closed]
- Open P&L: [estimate on open positions]
- Next run watching: [specific setups]

---
## Log Entries

[First entry will be written by the bot at the end of its first run]
