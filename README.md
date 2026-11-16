# tiny-chess-01 entry

A chess engine in 4,096 bytes of Python (`main.py`, packed from the
readable source with `makefun compress`: `node <kit>/cli/makefun.mjs
compress engine.py --out main.py`, main.py now 3,927 bytes).

## Design

- 0x88 board, pseudo-legal move generation with make/unmake and
  king-safety legality test. Castling, en passant and promotions.
- Negamax alpha-beta with iterative deepening to a fixed node budget
  (150,000 nodes a move), never a clock, so trials are deterministic
  and replay identically.
- Quiescence search over captures and promotions, and over every
  reply when in check.
- Transposition table, killer moves and MVV-LVA capture ordering;
  null-move pruning (depth >= 3, non-pawn material present, not in
  check).
- Evaluation: material plus formula piece-square tables, a
  middlegame/endgame king term and the bishop pair.
- Repetition and fifty-move draws scored 0 inside the search, with
  the repetition table seeded from the game's moves (so a position
  already seen in the game counts, including the root position).
  The Zobrist hash covers castling rights and the en passant square,
  so transpositions can no longer collide across them.
- Quiescence returns 0 for stalemate (in the endgame, where the
  search can reach it); a quiet position with legal moves still
  returns its static evaluation.
- Talks to the env only (`MAKEFUN_ENV_URL`): `POST /game`, `/move`.
  No model calls, no other network, no other files.

## Files

- `main.py` — the whole engine (3,927 bytes).
- `engine.py` — the readable source main.py was packed from. It lives
  in this folder but is deliberately NOT committed: the whole snapshot
  is capped at 8,192 bytes, and committing the source would break the
  cap. `makefun bundle` sends it beside the sessions from the
  compress record instead.
- `LICENSE` — MIT.
