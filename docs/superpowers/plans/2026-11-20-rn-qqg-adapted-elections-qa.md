# RN 3.2 — electoral QA report

Date: 2026-11-20
Branch: `feat/qqg-rn-election-system`
Base: `fix/society-poverty-indicator`

## Implemented
- QQG-inspired electoral sequence retained: PASO, party-only vice selection, general campaign, general scrutiny and runoff.
- Six fictional opposition tickets added, each with a running mate, ideology, profile, platform, baseline support and territorial affinities.
- Custom-party PASO roster now uses a distinct fictional internal slate rather than reusing cabinet names.
- General campaign offers seven strategic actions while preserving the four-actions-per-quarter cap.
- General election scrutiny now allocates votes by provincial population and applies regional candidate modifiers.
- Runoff support now includes partial transfers based on ideological compatibility.
- Election UI now displays ticket pairings, party, ideology/profile and district-level results.
- Version label advanced to 3.2.
- No new mandatory save fields were introduced; older candidate and district records use safe fallbacks.

## Tests performed in an isolated Firecrawl browser session
- Fresh custom campaign created with a fictional party and candidate.
- PASO campaign: all four quarterly actions executed; PASO opened on schedule; scrutiny completed; player candidate won.
- Vice selection: selected a figure from the same party; selector contained only same-party figures.
- General campaign: seven available strategic actions displayed; four distinct actions used; fifth action unavailable for that quarter.
- General election: opened in year 3 quarter 1 with seven tickets and 24 provincial districts.
- Runoff: first round had seven candidates; second round had two candidates; final round completed.
- Final test result: 42,000 national votes; sum of district votes also 42,000; all 24 districts reported. Test-only result: Test Candidate 59%, Alianza Federal 41%.
- Two-term limit: incumbent with two consecutive terms was excluded from PASO; successor was marked player-controlled.
- Legacy-save shape: five-candidate general-election save without new metadata loaded and began scrutiny; missing vice/profile/region/population fields did not crash the page.
- Responsive regression: all nine views checked at 320, 390, 768 and 1440 px; no horizontal overflow.
- JavaScript syntax check passed with `new Function` on the embedded script.

## Notes / limitations
- Vote outcomes are simulated and intentionally stochastic; the test result above is not a balance target or expected winner.
- District population weights are proportional approximations based on the game's existing population values.
- Regional affinities are game abstractions, not real-world polling or claims about actual Argentine voting behavior.
- Browser testing was performed against a preview deployment in an isolated session, not against the production site.
- Production must not be merged or deployed until review.
