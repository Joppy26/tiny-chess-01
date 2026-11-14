# tiny-chess-01

A strong chess engine in at most 4,096 bytes of Python (Python 3.13, stdlib only).

`main.py` is the whole engine: a single compressed payload (lzma + base85) that
unpacks at import time into a 0x88 board engine with:

- negamax PVS + transposition table (Zobrist) + quiescence search
- null-move pruning, late-move reductions, killer + history move ordering
- incremental tapered piece-square evaluation (material + PST + king safety)
- fixed 75,000 node budget per move (deterministic, no clock, no randomness)

It plays the make.fun tiny-chess-01 gauntlet: 16 games against 8 house engines
(alpha-beta depth 2/3/4 and 100k/250k/500k/1M/2M nodes per move), once per colour,
from book openings, talking to the env at `MAKEFUN_ENV_URL`.

## v6 changes (from the r2 engine)

- Quiescence search now considers all captures: the old "bad capture" skip
  (which dropped every capture where victim < attacker, even free ones) is gone.
- Null-move reduction softened from R=4/3 to R=3/2, so quiet lines are searched
  more accurately instead of being pruned away.
- Node budget raised from 50,000 to 75,000 per move (~2.2s/move, safe under
  the 10s/move limit). LMR (rd=2) and the king-safety taper + tempo eval stay.

## Local test results

- Perft: all 6 standard positions correct (depth 1-4) vs python-chess.
  Startpos depth 3 = 8902. Mates in 1 and in 2 are found.
- Eval integrity: incremental eval matches from-scratch eval over random games
  (30 games x 60 plies, make/unmake).
- Speed: ~25,000-37,000 nodes/sec (single CPU) under normal load.
- Promotion UCI verified: queen/ rook/ bishop/ knight all emit the correct
  letter (a past bug emitted 'k' for queen and broke endgames; fixed).
- The r2 engine measured Elo ~850 in a local 16-game trial (1W/2D/13L),
  below the 1,154 floor: it lost positionally, not tactically, because the
  old pruning misevaluated quiet lines. v6 is the fix for that weakness;
  its official grade comes from the make.fun practice run, not local trials.

## Layout

- `main.py` (4,042 bytes) - the engine, the only entrypoint.
- `README.md` - this file.
- `LICENSE` - MIT.
- `.makefun/owner.json` - ownership claim for the make.fun account.
