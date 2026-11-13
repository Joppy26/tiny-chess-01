# tiny-chess-01

A strong chess engine in at most 4,096 bytes of Python (Python 3.13, stdlib only).

`main.py` is the whole engine: a single compressed payload (lzma + base85) that
unpacks at import time into a 0x88 board engine with:

- negamax PVS + transposition table (Zobrist) + quiescence search
- null-move pruning, late-move reductions, killer + history move ordering
- incremental tapered piece-square evaluation (material + PST + bishop pair)
- fixed 40,000 node budget per move (deterministic, no clock, no randomness)

It plays the make.fun tiny-chess-01 gauntlet: 16 games against 8 house engines
(alpha-beta depth 2/3/4 and 100k/250k/500k/1M/2M nodes per move), once per colour,
from book openings, talking to the env at `MAKEFUN_ENV_URL`.

## Local test results

- Perft: all 6 standard positions correct (depth 1-4) vs python-chess.
- Eval integrity: incremental eval matches from-scratch eval over random games
  (30 games x 60 plies, make/unmake).
- Speed: ~25,000-37,000 nodes/sec (single CPU), depth 5-7 at 40,000 nodes.
- Promotion UCI verified: queen/ rook/ bishop/ knight all emit the correct
  letter (a past bug emitted 'k' for queen and broke endgames; fixed).
- Practice trial (trial-3, local env, fixed engine): beats the depth-2 house
  engine (win + draw), loses to depth-3 and above. Honest strength is around
  900-1000 Elo: a correct, legal, deterministic engine, not yet a prize
  contender. The main.py below is the exact engine that produced these numbers.

## Layout

- `main.py` (4,088 bytes) - the engine, the only entrypoint.
- `README.md` - this file.
- `LICENSE` - MIT.
- `.makefun/owner.json` - ownership claim for the make.fun account.
