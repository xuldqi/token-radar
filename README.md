# Token Radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/xuldqi/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/xuldqi/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: 198 pre-token protocols ranked by implied value, built from
a sealed DefiLlama snapshot (2026-11-14). Each protocol gets a model-derived
implied value, a category-comparable price, and a 7-step farm plan. Built by an
AI agent for make.fun's "Build the best token launch radar" bounty, $1,325 in USDC.

## The model (radar-2.1)

Implied value is priced against the live-token protocols in the same category,
computed from the snapshot itself at run time — no hardcoded names:

```
implied = round(tvl_usd × comp_multiple
  × (1 + clamp(change_7d, -40, +60) / 100 × 0.6)
  × (0.75 + min(age_days, 1095) / 1095 × 0.25)
  × (1 + 0.015 × min(chains − 1, 6)))
```

`comp_multiple` is the category median market-cap-to-TVL ratio of its live-token
protocols, shrunk toward the global median (4 pseudo-observations) and clamped
to 0.05–0.60. A category with fewer than two live comparables prices at the
global median, so one wild outlier cannot set a whole category. Multiples are
kept at full precision, so every row the page describes as priced at the global
median carries the same number. Each row's detail view names the largest
comparables it was priced against, and says plainly when a figure the model
needed (a 7-day change, a listing date) is missing from the snapshot and what
it assumed instead. The board covers all 40 watchlist protocols plus 158
pre-token protocols outside the watchlist, picked by implied value — including
a $2.76B protocol the watchlist rule left out.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path-to>/snapshot.json
```

Requires Node.js 22.18 or later. `main.ts` is the entrypoint: it reads the sealed
snapshot, writes `board.json`, `data.js` and `dossier.json`, and copies
`dashboard/` and `screenshots/` into the output directory. No network, standard
library only.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
