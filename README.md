# Tiny Chess — a chess engine in under 4 KB of Python

Entry for make.fun bounty **tiny-chess-01** ("Build the strongest chess engine
in 4 KB of Python"). `main.py` is **3,923 bytes** (limit 4,096); this repo is
kept under the bounty's 8,192-byte total cap on purpose — the readable sources
and full design notes live outside the submitted repo.

## How it plays

The program runs once per trial with `MAKEFUN_ENV_URL` set and plays the whole
8-game gauntlet over the env's HTTP API (`/game`, `/move`, `/resign`). Every
candidate move is cross-checked against the env's own `legal_moves` list, so an
illegal move can never be sent.

`main.py` is an lzma+base85 payload (explicitly allowed by the rules) that
unpacks a minified engine:

- 0x88 board, full legal move generation (castling through check, en passant,
  all promotions)
- Negamax with alpha-beta pruning and quiescence search (captures/promotions)
- Transposition table with seeded Zobrist hashing
- MVV-LVA capture ordering + killer moves; check extensions
- Material + piece-square tables with a phase-blended endgame king table
- **Fixed 30,000-node budget per move** — no clock, no unseeded randomness, so
  trials replay move-for-move identically

## Verification

- Perft: startpos depth 3 = 8,902, depth 4 = 197,281; Kiwipete depth 3 = 97,862
  (all exact)
- Tactics: mate-in-1 (both colours), a verified smothered mate-in-2, and a
  hanging-queen win are all found
- Full practice trial on the official kit env: **3 won, 0 drawn, 5 lost**,
  zero illegal moves, zero timeouts (mean move time ~1 s of the 10 s limit)
- **Practice Elo 1,253** on the official anchors — above the 1,154
  qualification bar for the final gauntlet

## Licence

MIT (see LICENSE).
