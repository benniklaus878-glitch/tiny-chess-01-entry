# Tiny Chess — a chess engine in under 4 KB of Python

Entry for make.fun bounty **tiny-chess-01** ("Build the strongest chess engine
in 4 KB of Python"). `main.py` is **4,066 bytes** (limit 4,096); the repo stays
under the bounty's 8,192-byte total cap — readable sources and full design
notes live outside the submitted repo.

## How it plays

The program runs once per trial with `MAKEFUN_ENV_URL` set and plays the whole
gauntlet over the env's HTTP API (`/game`, `/move`, `/resign`). Every candidate
move is cross-checked against the env's own `legal_moves` list, so an illegal
move can never be sent.

`main.py` is an lzma payload (explicitly allowed by the rules) that unpacks a
minified engine:

- 0x88 board, full legal move generation (castling through check, en passant,
  all promotions)
- Negamax with alpha-beta pruning, quiescence search, transposition table
  with seeded Zobrist hashing, null-move pruning and late-move reductions
- Full static-exchange (SEE) capture ordering plus a history heuristic for
  quiet moves, on top of TT-move and killer ordering; check extensions
- Material + piece-square tables with a phase-blended endgame king table,
  plus pawn-structure terms: bishop pair, doubled and isolated pawn
  penalties, passed-pawn bonuses by rank, and a king pawn shield
- **Fixed 55,000-node budget per move** — no clock, no unseeded randomness,
  so trials replay move-for-move identically

## Verification

- Perft: startpos depth 3 = 8,902, depth 4 = 197,281; Kiwipete depth 3 =
  97,862 (all exact); Zobrist incremental hash verified over random plies
- Tactics: mate-in-1 (both colours), a verified smothered mate-in-2, and a
  hanging-queen win are all found
- Measured against the previous entry's design on the official kit gauntlet
  under identical conditions on both practice trials: the fusion scored
  3.0 and 3.5 points of 6 against the previous design's 1.5 and 2.0
  (see the build transcripts)

## Licence

MIT (see LICENSE).
