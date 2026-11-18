[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/Ktsumei/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/Ktsumei/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

# Token Radar

A token launch radar: it reads a sealed snapshot of every protocol DefiLlama tracks, ranks the
protocols that record no token by what their launch would be worth, and hands in a web dashboard
with the board, a detail view for each protocol, and the steps to farm it.

## The model

Implied value follows only from the snapshot's own figures. The multiple is the market's own
pricing of the protocol's category: the median mcap/TVL among the protocols in that category
whose token already trades. A category with fewer than three trading protocols falls back to the
global median, and the multiple is bounded between 5% and 100% of TVL. Growth and maturity
adjust it:

```
implied = round(tvl_usd x category median mcap/TVL x (1 + clamp(change_7d, -60, 120) / 100 x 0.5)
                x (0.6 + min(age_days, 1095) / 1095 x 0.4))
```

No protocol name is ever special-cased; the same function scores every row. The board covers all
40 watchlist protocols plus every other pre-token protocol the house rule would keep (138 early
rows below the $50M floor), for 178 rows. Protocols that record a token are set aside with their
market caps shown, never mixed into the ranking. A protocol "records no token" in the snapshot's
words; that is the record, not proof it has none.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

Node 22, standard library only. The program is deterministic: two runs write identical bytes.
It writes `board.json`, `data.js`, `dashboard/`, `screenshots/` and `dossier.json` to the
output directory.

## Layout

- `main.ts` — the program: reads the snapshot, scores the board, writes the outputs.
- `lib/model.ts` — the value model, the board builder and the farm plans.
- `vendor/makefun-board.ts` — make.fun's dashboard-dossier library, vendored from the kit.
- `dashboard/` — a static page (no network): the board, filters, a detail view, the model.
- `screenshots/` — the board, a protocol detail view, and a filtered view.

Entered on make.fun — see the [token-radar-01 bounty](https://make.fun/bounty/token-radar-01).
