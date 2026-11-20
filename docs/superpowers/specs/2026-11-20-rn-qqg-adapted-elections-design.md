# RN electoral system — QQG-inspired, RN-native adaptation

## Product boundary
Use QQG only as a reference for the electoral sequence and player-facing clarity: party primaries, ticket formation, campaign, general election, district scrutiny and runoff. Do not reproduce its interface, assets, text, formulas or implementation. RN retains its own visual language and connects election outcomes to its economy, society, institutions and federal structure.

## Current implementation findings
- Existing stages: primary campaign, PASO scrutiny, vice selection, general campaign, general scrutiny/runoff, government.
- Existing save data already records election phases, primary candidates, campaigns, results, districts and term limits.
- General election currently uses four fixed opposition tickets.
- General scrutiny assigns each vote to a random district, without meaningful population or regional differences.
- Candidate detail is thin; opposition formulas do not expose a running mate or platform in the ballot UI.
- Campaign actions are limited and do not clearly distinguish policy, territorial and coalition strategies.

## Design
1. Preserve the existing stages, calendar, save key and legacy state fields.
2. Build a richer set of fictional opponent tickets with party, vice-presidential candidate, ideology, platform, baseline support and territorial profile. Keep the user's selected party/candidate intact.
3. Make candidate strength depend on RN state: approval, stability, jobs, inflation, poverty, institutional trust and campaign actions. Avoid deterministic winners.
4. Make district scrutiny meaningful: vote batches should be allocated using province population weights and regional profile modifiers; show district progress and results.
5. Keep vice selection restricted to figures in the user's party roster; explain why the choice matters without making it a purely cosmetic field.
6. Expand campaign choices so players trade treasury resources among territorial campaigning, debates, coalition agreements, policy platform, social dialogue and transparency. Keep the existing per-quarter action limit.
7. In a runoff, transfer part of eliminated candidates' support according to ideological proximity and coalition compatibility; do not simply reset the two remaining candidates to equal odds.
8. Display each ticket's vice, party, ideology and short platform. Clearly mark fictional simulated opposition tickets.
9. Preserve the two-consecutive-term succession rule and all existing save compatibility.
10. Do not merge or deploy production. Use a separate feature branch and preview deployment.

## QA acceptance
- Existing saves from pre-election and government phases still load.
- New campaign can complete PASO, choose an eligible same-party vice, campaign, general election and runoff.
- A two-term incumbent is excluded from PASO and the successor remains player-controlled.
- District vote totals sum to national vote totals; district populations influence vote allocation; no district receives invalid/negative values.
- All campaign actions respect the per-quarter cap and cannot be repeated in the same quarter.
- Reloading during campaign, PASO scrutiny and general scrutiny preserves state.
- No NaN/Infinity, JS exceptions, broken buttons or mobile horizontal overflow at 320–390px.
- Production remains unchanged until user review.
