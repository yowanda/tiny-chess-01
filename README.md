# tiny-chess-01: a 4 KB Python chess engine

An original chess engine in a single `main.py` of 4,054 bytes. It plays
every game through the env at `MAKEFUN_ENV_URL` and picks its move by
search, never by clock: a fixed 50,000-node budget per move, so a live
trial and its replay play the same moves.

## How it plays

- 0x88 board, negamax alpha-beta with iterative deepening.
- Quiescence over captures and promotions; transposition table with
  killer moves and history ordering; null-move pruning and futility
  pruning. No PVS and no LMR: both were tried and measured worse in
  local gauntlets, so they were removed. PVS plus LMR scored 3.0/16
  (Elo 933 area) and a plain-PVS variant 2.5/16, against 8.0/16 for
  this plain alpha-beta version — the reductions corrupted the search
  instead of speeding it up, so the engine keeps the simple search
  and spends its bytes on the evaluation instead.
- Evaluation: material plus PeSTO middlegame piece-square tables,
  bishop pair, pawn structure (passed, doubled, isolated pawns),
  tempo bonus, pawn shield, and a move-count safety net that never
  returns a move outside the env's legal list.

## Strength

Measured locally on the practice gauntlet (16 games, 8 house engines, no crash, no time forfeit):
8.0/16, 1,396 Elo (official MLE against the gauntlet anchors) — above
the sunfish 4 KB baseline of 1,360. Wins against nodes-100k, nodes-250k,
nodes-500k and nodes-1M in the test run.

## Run

The engine needs the trial env; it reads `MAKEFUN_ENV_URL` and loops
`POST /game` then `POST /move` until every game is done. It uses only
the Python standard library and reads no other file.

## License

MIT. See LICENSE.
