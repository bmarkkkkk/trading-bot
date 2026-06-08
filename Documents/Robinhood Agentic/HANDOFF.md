# HANDOFF: Build a Reliable Autonomous Trading Routine on Claude Cloud

You are receiving this from a previous local Claude Code session that built a complete autonomous trading framework but hit a wall on reliable execution. The local Claude Code scheduler is firing only ~20% of scheduled runs, which is unacceptable for a trading agent. The owner needs you to rebuild this as a **cloud-hosted scheduled routine** that runs independently of any local machine state.

This document is **fully self-contained** — you have everything you need to execute. Read it end to end before acting.

---

## 1. Your Goal

Set up a **reliable, cloud-hosted scheduled task** that runs every 30 minutes during US market hours (Mon–Fri, 9:00 AM – 4:30 PM Eastern, which is 6:00 AM – 1:30 PM Pacific) and autonomously trades the owner's Robinhood account using the trading framework in Section 7 below.

The owner has explicitly granted full autonomy — the routine should execute trades without confirmation prompts. The framework itself has hard risk rules baked in (15% daily loss limit, no averaging down, etc.) which provide the guardrails.

---

## 2. Why This Handoff Exists

A local Claude Code scheduled task was set up for this exact purpose. It works mechanically — the prompt is solid, the Robinhood MCP connector is authenticated, manual triggers work. But the scheduled-tasks system inside the desktop Claude Code app is not firing reliably:

- **6:00 AM PDT slot:** ✅ Fired
- **6:30, 7:00, 7:30, 8:00 AM PDT slots:** ❌ All skipped despite the app being open and laptop awake

Result: 1 of 5 expected runs actually executed. The cron expression is correct, the task is enabled, the prompt is functional — the issue is internal to the local scheduler.

The owner does not have access to `claude.ai` Routines via the standard sidebar (checked — not visible on their account). So we need you to set this up using whatever cloud-execution mechanism IS available in your environment (Claude.ai routines if they exist on your plan, Anthropic API + a cloud cron, GitHub Actions, etc.).

---

## 3. Account Details (Robinhood)

- **Account number:** `461754046`
- **Account type:** Cash account (nickname: "Agentic")
- **`agentic_allowed: true`** — this account is approved for AI-placed trades
- **Account settlement:** Instant Settlement enabled. Proceeds from sales are immediately available to reinvest. No T+1 wait, no PDT restrictions.
- **Options approval:** Level 2 (buy calls and puts; no naked selling)
- **Starting capital:** $900 in buying power, $1,000 pending deposit. Total expected ~$1,900 once settled.
- **The owner has another margin account (••••0319)** that is NOT `agentic_allowed`. Do not touch it. Only the `461754046` account is in scope.

---

## 4. Required Connectors / Tools the Routine Needs

For the routine to function, the executing Claude session needs access to:

### MCP Tools (Robinhood connector)
- `get_portfolio` — buying power and account value
- `get_equity_positions` — current holdings
- `get_equity_quotes` — real-time quotes (batch up to 20 symbols)
- `get_equity_tradability` — verify symbol can be traded before ordering
- `review_equity_order` — pre-trade simulation/validation
- `place_equity_order` — execute orders
- `get_equity_orders` — order history
- `cancel_equity_order` — cancel pending orders

The owner has the Robinhood MCP connector authenticated locally. **Verify it's also connected on whatever cloud environment you set up.** If you're operating on `claude.ai`, the user must add the Robinhood connector there too (Settings → Connectors → Robinhood → OAuth flow).

### Standard Web Tools
- `WebSearch` — for market scanning
- `WebFetch` — for Finviz chart data and CBOE put/call ratio

### Optional / Notes
- `place_options_order` / `get_options_chain` — NOT currently present in the Robinhood MCP connector. If they get added later, the framework prompt is already written to use them. Until then, the routine will execute equity-only trades and note this in its log.

---

## 5. Schedule Requirements

- **Frequency:** Every 30 minutes
- **Days:** Monday – Friday
- **Time window:** 6:00 AM – 1:30 PM US Pacific Time (covers 9:00 AM – 4:30 PM US Eastern, full market session plus pre-market prep and post-close cleanup)
- **Cron equivalent (Pacific Time):** `*/30 6-13 * * 1-5`
- **Cron equivalent (Eastern Time):** `*/30 9-16 * * 1-5`
- **Cron equivalent (UTC, accounts for PDT = UTC-7):** `*/30 13-20 * * 1-5`

If your scheduling system requires UTC, use the UTC version. If it accepts local time, use Pacific.

Each run is independent — the routine prompt is fully self-contained and assumes no memory of prior runs. The agent reads current portfolio state at the start of each run and reasons from there.

---

## 6. Existing Assets (already saved locally — may be useful)

- **Massive.com API key** (formerly Polygon.io) is saved at `~/.claude/secrets/massive_token.txt`. Verified working but free tier does NOT include options chain snapshots (gated to paid Advanced tier at $199/mo). The reference endpoint (which contracts exist) works on free tier but isn't critical for the framework. Token included here for completeness:
  ```
  9N3LRQdZUgGTDcY8HvbmzObpnvFItctp
  ```
  The framework currently does not require this token to function. If you wire up a cloud setup, you can pass it through if relevant or ignore it.

---

## 7. The Full Trading Framework Prompt (Paste This Into the Routine)

The complete agent prompt is in `TRADING_PROMPT.md` in this same directory. It is ~330 lines of refined trading methodology covering:

- Account details and rules
- Active-trading mission (full autonomy, capture volatility)
- Speed/efficiency directives (parallelize everything)
- Time-of-day playbook (pre-market, open, midday, power hour)
- Options strategy with liquidity gates
- Execution intelligence (limit orders, no chasing)
- Pure TA-first methodology (charts pick the trades, news confirms)
- Strict MA verification rules (the key disqualifier — price must be ABOVE 20 AND 50 SMA for any long; below = falling-knife trap)
- Catalyst confirmation (hard events only — no Twitter, no 13Fs)
- Market environment check (SPY, VIX, sector rotation, CBOE put/call ratio)
- Sizing tiers (A+/A/B based on setup quality)
- Compounding plan as the account grows
- Hard risk rules (15% max daily loss, no revenge trading, no averaging down)

**Use the contents of `TRADING_PROMPT.md` verbatim as the routine's prompt.** Strip the YAML frontmatter (the `---` block at top with `name:` and `description:`) when pasting if your routine UI doesn't accept it.

---

## 8. What You Need to Build / Decide

Choose the path based on what's actually available in your environment:

### Path A — Claude.ai Routines (preferred if available)
1. Check if Routines / Scheduled Tasks is available in your claude.ai sidebar or settings
2. If yes: create a new routine with the schedule from Section 5 and the prompt from Section 7
3. Verify the Robinhood connector is enabled on the claude.ai account
4. Run it manually once to confirm execution works end-to-end (no orders should fire pre-market — that's the framework's PREP slot)

### Path B — Anthropic API + Cloud Cron
If Routines isn't available, the reliable alternative is:
1. Write a Python script that calls the Anthropic Messages API with the prompt from Section 7
2. The script needs MCP server connections for Robinhood — this is the tricky part since the Robinhood MCP is OAuth-gated through Claude's connector system, not publicly available
3. Schedule the script via: GitHub Actions cron (free tier), AWS Lambda + EventBridge, or a small $5/mo VPS with crontab
4. Important: the script needs to handle MCP server auth, which may not be feasible without going through Claude's connector OAuth flow

### Path C — Honest Assessment
If neither Path A nor Path B is feasible with the resources at hand, tell the owner directly. The realistic fallback is upgrading to Claude Pro/Max ($20–$200/mo) for the cloud features that enable this. The owner explicitly does not want to do a bunch of manual triggers, so a fallback that requires constant babysitting should be flagged as such.

---

## 9. Test Plan (Run After Setup)

Once the routine is created:

1. **Manual run #1 — pre-market hours**
   - Trigger immediately
   - Expected behavior: framework Step 1 (state check) executes, Step 3 (scanning) runs, but Step 4 (execution) does NOT — the time-of-day playbook says pre-market is PREP only
   - Verify: portfolio state is read, no orders are placed, log output describes candidates the agent is watching for the open

2. **Wait until next market-open Monday**
   - First scheduled run at 6:00 AM PDT / 9:00 AM ET
   - Expected: pre-market prep, no orders
   - 6:30 AM PDT / 9:30 AM ET — market open run — this is when first trades may fire if A+ setups exist

3. **Verify reliability**
   - Track which scheduled slots actually fire over the first 2–3 trading days
   - Target: >95% fire rate (vs. local Claude Code's 20%)
   - If you see misses, that's the same class of problem we had locally — diagnose and fix

---

## 10. Risk Acknowledgment to Share with the Owner

Before flipping the routine live, remind the owner:

- This routine will trade **real money** without confirmation prompts
- The framework has risk caps (15% daily loss limit, no averaging down, position concentration rules) but no software can guarantee no loss
- Tail-risk events (flash crashes, halts, exchange outages) are possible and not fully hedged against
- The routine is set up to be aggressive — favors options for volatility, runs concentrated positions in a small account
- Owner should monitor outputs daily for the first 1–2 weeks and be ready to disable the routine if behavior drifts

---

## 11. After You're Done

Send the owner a summary message with:
- Which path you took (A, B, or C)
- The routine URL or identifier for managing it later
- Schedule timezone and exact cron expression actually used
- Whether the connector is connected and verified
- Results of the manual test run
- Any caveats or follow-up needed

If you took Path C (honest "can't do it"), be specific about why and what the realistic next step is.

---

End of handoff. Begin work.
