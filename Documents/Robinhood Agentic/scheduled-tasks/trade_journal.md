# Trade Journal — Active Positions & Theses

This file is the agent's working memory across runs. Each run READS this at the start (to manage positions coherently) and UPDATES it on every entry, exit, or thesis change. Keep it current — stale entries cause bad decisions.

## Format per open position:
```
### [TICKER] [CALL/PUT/EQUITY] — opened YYYY-MM-DD HH:MM ET
- Instrument: <strike + expiration for options, or shares for equity>
- option_id: <UUID if option>
- Entry: <price> | Size: <contracts/shares> | Setup grade: <A+/A/B>
- Thesis: <one line — why this trade, what setup>
- Stop: <price/premium> | T1: <price> | T2: <price>
- Delta at entry: <x> | IV at entry: <x>
- Status: <latest note>
```

---

## OPEN POSITIONS

(none logged yet — agent will populate)

---

## CLOSED TODAY
(cleared each new trading day)
