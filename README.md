# Dip-scalp cbBTC bot — btc-race-24h-20261123-0511

A trading bot for make.fun's "Race a BTC trading bot live for 24 hours"
(`btc-race-24h-20261123-0511`). It trades Coinbase Wrapped BTC (cbBTC) against
USDC on 1-minute on-chain bars, starting with $1,000 of paper money, long only.

## Strategy

Bitcoin's 1-minute tape mean-reverts after sharp intraday dips while the slower
trend stays intact. The bot:

- **Buys a dip** when the close is at least **0.8% below the trailing 90-bar
  high** and the **240-bar return is no worse than -3%** (no catching a
  collapsing market).
- **Exits** at **+1.0%** profit target, **-1.2%** hard stop, or a **0.4%
  trailing stop** once the trade is **+0.5%** in the money.
- **Full size** (100% of cash) per entry, at most **5 entries** per window,
  at least **20 bars** cooldown after each exit, one open position.
- **Fallback:** if the window grinds up for 12 hours without a single dip
  (240-bar return above +0.6%), it takes one trend long instead of sitting in
  cash all day.

All arithmetic is integer (`bigint`) on the harness's own price units, so two
runs on the same bars produce identical orders. No network, no wall clock, no
randomness, no files.

## Local dev grade (kit driver, process)

```
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot main.ts --driver process
```

Dev window (24h ending at the competition open, BTC +3.6%): **net PnL
+$40.45, 5 round trips, 5 wins, max drawdown 1.63%, fees $4.09** (local
simulator; see grade-local output for the official figure). House baselines
on the same window all lose money except buy-and-hold (+$32.17).

## Files

- `main.ts` — the bot (Node 22, erasable TypeScript)
- `vendor/makefun-bars.ts` — the kit's bars-replay shim, copied byte for byte
