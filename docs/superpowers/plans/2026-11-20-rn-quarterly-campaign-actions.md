# RN quarterly campaign actions implementation plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Renew four distinct campaign actions each quarter during PASO and general-election campaign phases.

**Architecture:** Reuse the existing pasoActions and electionActions arrays. Clear the relevant array after a quarter closes only when the resulting phase is still a campaign phase; display the four-action allowance in the election UI.

**Tech Stack:** HTML, vanilla JavaScript, existing localStorage save structure, Firecrawl isolated-browser QA.

**Spec:** docs/superpowers/specs/2026-11-20-rn-quarterly-campaign-actions-design.md

## Global Constraints
- Keep RN's quarterly calendar.
- Preserve existing action keys, costs, effects, and election formulas.
- Keep action costs deducted from the existing game treasury.
- Do not add required save fields or change localStorage keys.
- Keep production and main unchanged.

## Review Focus
- PASO actions reset after a quarter only if the phase remains primary_campaign.
- General campaign actions reset after a quarter only if the phase remains general_campaign.
- The quarter that starts PASO voting must not restore campaign actions.
- The quarter that starts general voting must not restore campaign actions.
- Legacy saves with used actions remain valid and refresh at the next campaign quarter close.

---

### Task 1: Verify the current one-use-per-election behavior

**Files:**
- Test: isolated Firecrawl browser session on the Society preview.

- [ ] **Step 1: Reproduce the current limitation** — use a PASO action, close one quarter, and inspect pasoActions.
- [ ] **Step 2: Confirm RED** — the used action remains in the array after the quarter advances.

### Task 2: Implement quarterly action renewal

**Files:**
- Modify: index.html, campaign action constants, electionActionsMarkup(), runPasoAction(), runElectionAction(), and closeQuarter().

**Interfaces:**
- Consumes: existing state.pasoActions, state.electionActions, state.electionPhase, and state.quarter.
- Produces: same save arrays, with per-quarter renewal.

- [ ] **Step 1: Define the four-action limit** — add CAMPAIGN_ACTIONS_PER_QUARTER = 4.
- [ ] **Step 2: Guard action execution** — reject another campaign action if four have already been used in the current phase/quarter.
- [ ] **Step 3: Reset PASO actions** — clear pasoActions after quarter transition only if the resulting phase is primary_campaign.
- [ ] **Step 4: Reset general actions** — clear electionActions after quarter transition only if the resulting phase is general_campaign.
- [ ] **Step 5: Display the allowance** — show used/available actions and explain renewal at quarter close in both campaign phases.
- [ ] **Step 6: Run syntax and browser verification** — verify reset, action costs, score changes, phase transitions, and save/reload.

### Task 3: Verify edge cases

**Files:**
- Test: isolated Firecrawl browser session on the new Vercel preview.

- [ ] **Step 1: Verify PASO renewal** — after using one PASO action, quarter close resets the list while remaining in primary campaign.
- [ ] **Step 2: Verify general renewal** — after using one general action, quarter close resets the list while remaining in general campaign.
- [ ] **Step 3: Verify phase transition** — quarter close that starts voting does not restore campaign actions.
- [ ] **Step 4: Verify old-save compatibility** — an older save with action arrays still loads and can advance.
