# Tiny Chess Engine — v6

A tiny chess engine for the tiny-chess-01 competition, squeezed into a single compressed line of Python.

## How it plays

The engine searches 75,000 nodes per move. It uses a 0x88 board representation with negamax search, principal variation search (PVS), a transposition table, and quiescence search.

The v6 fix is in quiescence search: it now considers ALL captures. Earlier versions skipped bad captures there, which threw away tactical information; removing that skip was worth real strength.

Other search features:

- Null-move pruning with R=3/2
- Late-move reduction (rd=2, kept from earlier versions)
- Transposition table and PVS on top of negamax

## Strength

Local baseline testing puts the engine at roughly Elo 850. The r2 graded result was Elo 0, and the floor of 1,154 was not cleared, so there is still a long way to go — v6 is a step in the right direction rather than a finished product.

## Size

The whole entry is one line: the engine source is compressed with lzma and encoded in base85, then unpacked and executed at startup. The file is 4,042 bytes against a 4,096-byte limit.

## Verification

Move generation was checked with perft counts on the verified engine. From the start position: depth 1 gives 20 nodes, depth 2 gives 400, and depth 3 gives 8,902. From the Kiwipete test position: depth 1 gives 48, depth 2 gives 2,039, and depth 3 gives 97,862. The engine also finds mate in one, including Scholar's mate with Qh5xf7.
