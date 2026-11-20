# RN quarterly campaign-action design

## Intent
Make RN's campaign strategy more granular by adapting QQG's limited-action campaign loop to RN's quarterly simulation calendar.

## Goals
- Allow four distinct campaign actions during each active campaign quarter.
- Refresh the four-action allowance when a quarter closes, only while the game remains in a campaign phase.
- Keep the existing PASO and general-election action choices, effects, costs, and score calculations.
- Display used/available actions and explain when they refresh.
- Preserve old saves without adding a required save field.

## Non-goals
- Do not change the quarterly national simulation calendar to monthly.
- Do not copy QQG's code, names, text, or assets.
- Do not add a separate campaign budget in this pass; existing campaign action costs continue to be deducted from the game's treasury.
- Do not change election dates, candidate selection, vice selection, or vote formulas.

## Approaches
1. **Monthly campaign calendar:** match QQG's four actions per month exactly. Rejected because RN's entire macro simulation advances quarterly; adding monthly political turns would desynchronize the election system from the rest of the game.
2. **Four actions per RN campaign quarter:** renew the existing four distinct actions after each quarter while the phase remains primary_campaign or general_campaign. Chosen because it fits RN's time model and reuses existing actions, costs, and save arrays.
3. **Keep one use per election:** lowest risk, but does not address the strategic depth gap identified in QQG.

## Design decisions
- The four available actions remain the four existing PASO actions or four existing general-campaign actions.
- Score caps and candidate-weight multipliers scale together: PASO cap 18 → 36 and weight 2.7 → 1.35; general campaign cap 20 → 40 and weight 2.6 → 1.3; runoff weight 1.2 → 0.6. This preserves the previous maximum electoral influence while allowing repeated quarterly actions to remain useful.
- Each action can be used once in the current quarter.
- The action arrays are cleared after quarterly phase transitions, only if the resulting phase is still a campaign phase.
- If a quarter closure starts PASO voting or general voting, the phase transition takes precedence and no new campaign actions are granted.
- Action costs continue to be paid from the current game treasury, with no new resource or save field.

## Risks
- Campaigns can become more expensive because players can invest repeatedly across multiple quarters. This is intentional in this pass; treasury costs remain visible and can be rebalanced after playtesting.
- The score cap/multiplier relationship must be kept in sync if action bonuses or campaign duration change.
- Older saves retain their current-quarter action usage until the next quarter closes; no progress is discarded during migration.
