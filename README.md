# Skip-Bo Solo

A single-player Skip-Bo game — you vs. a computer opponent — built as one
self-contained `index.html` file. No build step, no backend, no accounts.

## Rules implemented

Genuine Skip-Bo rules (not Spite & Malice):

- 162-card deck: 144 number cards (1–12, ×12 each) + 18 Skip-Bo wild cards.
- 30-card stock pile each (only the top card visible/playable).
- 5-card hand, drawn back up to 5 at the start of each turn.
- 4 personal discard piles each, face-up, top card only playable.
- Any number of shared center build piles, each built strictly 1 → 12;
  Skip-Bo cards are wild. A pile that reaches 12 clears into a reshuffle
  pool.
- Play as many cards as you can each turn, in any order, from your stock,
  hand, or your own discard piles. End every turn by discarding exactly
  one hand card onto one of your own discard piles.
- First to empty their stock pile wins.
- If the draw pile and the completed-pile pool are both empty, cards are
  pulled from beneath the in-progress center piles as a last resort
  (piles keep their current progress) — logged clearly in the play log.
  In the vanishingly rare case nothing is left anywhere, the game is
  called a stalemate so it always resolves.

## Computer opponent

Fully local, deterministic, rule-based JavaScript — evaluates every legal
move with a heuristic scorer plus a short greedy rollout simulation to
look for chains that keep emptying its stock pile. **No network or LLM
calls of any kind happen at runtime** — the bot works forever, offline,
with no dependency on Claude or any API.

## Undo

An Undo button reverts the most recent play/discard from the current
human turn (snapshot-based). It's cleared at the start of each of your
turns and never touches the computer's moves or prior completed turns.

## Scoreboard

Recorded automatically the instant a game ends via `window.storage`
(a small localStorage-backed wrapper) — wins/losses, streaks, and a
recent-results history, persisted only in this browser.

## Deploy

Static site — deploys as-is on Cloudflare Pages (no build command, root
output directory).
