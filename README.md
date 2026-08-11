# Spite & Malice Solo

A single-player card game — you vs. a computer opponent — built as one
self-contained `index.html` file. No build step, no backend, no accounts.

## Rules implemented

Two standard decks plus four jokers (house ruleset, per Adam's spec):

- 108-card deck: 2 standard 52-card decks + 4 jokers. No special numbered
  deck.
- 15-card stock pile each (house rule), only the top card visible/playable.
- 5-card hand, drawn back up to 5 at the start of each turn.
- 4 personal discard piles each, face-up, top card only playable — **never
  reshuffled, ever, under any circumstance.**
- Up to 12 shared center build piles, each built strictly Ace through King
  (13 ranks). Jokers (all 4) are wild and can stand in for any needed rank.
  Kings are ordinary cards — not wild.
- A pile finished with a King clears off the table into a reshuffle pool.
- Play as many cards as you can each turn, in any order, from your stock,
  hand, or your own discard piles. End every turn by discarding exactly
  one hand card onto one of your own discard piles.
- First to empty their stock pile wins.
- Draw-pile-empty fallback order: (1) reshuffle the completed-King-pile
  pool; (2) only if that's also empty, strip the top card off each center
  pile and shuffle everything underneath into a fresh draw pile — each
  pile tracks its rank (`base` + physical card count) separately from its
  card count, so this never corrupts a pile's progress. Discard piles are
  never touched by either fallback.
- Scoring when a game ends: the winner scores the point value of
  everything left in the loser's stock pile — wild (joker) = 20, face
  card (J/Q/K) = 10, ace = 1, everything else its number. No flat bonus
  for winning.

## Computer opponent

Fully local, deterministic, rule-based JavaScript — no network or LLM
calls of any kind happen at runtime.

- Its own stock card, whenever legally playable, is always played
  immediately — choosing the **least-generous legal pile** (the most
  "spent"/highest-value eligible pile) when a wild stock card could go to
  several.
- Every other move (hand and discard-top cards) is chosen via a
  **full-turn rollout search**: every legal move is tried, the rest of
  that turn is played out greedily on a cloned position, and the line
  that moves the most stock cards — then leaves the best evaluated
  position — is kept.
- The evaluator rewards distinct reachable ranks ("options"), rewards
  discard piles that read as a descending playable run from the top
  (e.g. 8,7,6), penalizes "clutter" cards that can't reach any pile and
  don't help the stock card, and values holding an ace or joker in
  reserve to open a fresh pile later.
- Target piles for hand/discard plays are chosen by proximity to the
  bot's **own** stock rank.
- The bot never plays defensively or blocks the human — it never
  references the human's hand, stock, or discard piles when deciding a
  move, with the single exception of its forced-priority stock play
  above.

## Undo

An Undo button reverts the most recent play/discard from the current
human turn (snapshot-based). It's cleared at the start of each of your
turns and never touches the computer's moves or prior completed turns.

## Scoreboard

Recorded automatically the instant a game ends via `window.storage`
(a small localStorage-backed wrapper) — wins/losses, cumulative points,
streaks, and a recent-results history, persisted only in this browser.

## Deploy

Static site — deploys as-is on Cloudflare Pages (no build command, root
output directory).
