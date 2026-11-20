# RN presidential succession and term-limit design

## Problem found
The election flow stored a two-consecutive-term flag, but the next PASO still generated the incumbent as a player-controlled candidate. The general-election metadata marked the election as forced without actually replacing the term-limited incumbent.

## Goals
- Enforce the existing two-consecutive-term limit for an incumbent player-president.
- Keep the player's party in control of its succession process.
- Exclude the outgoing president from the next PASO candidate list.
- Present a new party figure as the player-controlled succession candidate.
- If that party nominee wins the general election, continue the player's government campaign with the successor and reset the personal consecutive-term counter to one.
- Keep save compatibility by deriving eligibility from existing fields; no new save key.

## Decisions
- Eligibility is determined by existing `inaugurated && consecutiveTerms >= 2`.
- The outgoing president is filtered from the party roster.
- The first eligible party-roster figure becomes the player-controlled succession candidate; other party figures remain internal rivals.
- The term-limited election is marked `forced`.
- A forced general-election ticket remains player-controlled because the player is managing their party's succession, even if another party figure won the PASO.
- If the succession ticket wins, the new president starts a new consecutive-term count at one.

## Non-goals
- No change to election calendar, voting thresholds, district counts, or vote-generation formulas.
- No new save fields.
- No automatic merge or production deployment.
