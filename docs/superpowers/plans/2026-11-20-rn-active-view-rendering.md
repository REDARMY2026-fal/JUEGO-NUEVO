# RN active-view rendering optimization implementation plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Reduce unnecessary hidden-screen DOM updates without changing simulation behavior or save compatibility.

**Architecture:** Keep the single-file HTML app. Refresh shared header metrics on every render and dispatch view-specific DOM work only to the active view. Keep the existing dedicated election-scrutiny render calls.

**Tech Stack:** HTML, CSS, vanilla JavaScript, GitHub Pages, Firecrawl isolated-browser QA.

**Spec:** `docs/superpowers/specs/2026-11-20-rn-active-view-rendering-design.md`

## Global Constraints
- Preserve the current single-file architecture.
- Do not change gameplay formulas, campaign cadence, save schema, or localStorage keys.
- Preserve the Society PR branch and keep production untouched.
- Keep all nine view identifiers unchanged.

## Review Focus
- Navigating to a previously hidden view must populate it with current state.
- Quarter close must refresh the overview and not leave stale metrics.
- Policy actions must refresh the active view and global header.
- PASO/general-election timers must keep updating their own screens.
- Existing saves and legacy company records must remain loadable.

---

### Task 1: Capture the render regression

**Files:**
- Test: isolated Firecrawl browser session against the current Society preview.

**Interfaces:**
- Observe hidden view containers `#industryBars`, `#macroKpis`, `#provinceGrid`, and `#historyList` while the Society view is active.

- [ ] **Step 1: Record the failing behavior** — attach MutationObservers to hidden-view containers, execute one Society policy, and record that hidden views are mutated.
- [ ] **Step 2: Confirm the expected failure** — verify mutation counts are greater than zero before the refactor.

### Task 2: Dispatch render work by active view

**Files:**
- Modify: `index.html`, `function render()`.

**Interfaces:**
- Consumes: existing `activeView`, `bar()`, `alerts()`, `macroActions()`, `renderActions()`, `renderMacro()`, `renderFinance()`, `renderPrivateCompanies()`, `renderProvinces()`, `renderElections()`, and `renderPolitics()`.
- Produces: unchanged `render()` entry point used by existing actions and `setView()`.

- [ ] **Step 1: Keep shared header refresh** — date, top metrics, government identity, party logo, and phase continue updating on every call.
- [ ] **Step 2: Render overview only when `activeView === "overview"`** — national bars, alerts, budget estimates, and overview notice.
- [ ] **Step 3: Render economy only when `activeView === "economy"`** — macro actions, analysis, and charts.
- [ ] **Step 4: Render finance only when `activeView === "finance"`** — existing finance renderer.
- [ ] **Step 5: Render industry only when `activeView === "industry"`** — production bars/actions and private companies.
- [ ] **Step 6: Render provinces only when `activeView === "provinces"`** — existing province renderer.
- [ ] **Step 7: Render society only when `activeView === "society"`** — social indicators and policy actions.
- [ ] **Step 8: Render government only when `activeView === "government"`** — politics renderer, identity, actions, and institutional bars.
- [ ] **Step 9: Render elections only when `activeView === "elections"`** — existing election renderer and listeners.
- [ ] **Step 10: Render history only when `activeView === "history"`** — existing history list.
- [ ] **Step 11: Run syntax validation and the isolated browser suite** — verify all nine screens, active policy updates, quarter close, save/reload, and old-save migration.

### Task 3: Verify no regression

**Files:**
- Test: isolated Firecrawl browser session against the new Vercel preview.

- [ ] **Step 1: Verify hidden views are not mutated** — repeat the Task 1 observer test; expected mutation count is zero for hidden containers.
- [ ] **Step 2: Verify navigation freshness** — visit all nine views and confirm each displays current values.
- [ ] **Step 3: Verify gameplay and persistence** — execute one policy, close a quarter, save/reload, and load a legacy-format save.
- [ ] **Step 4: Verify responsive layout and runtime errors** — check 390 px and desktop widths, horizontal overflow, and page errors.
- [ ] **Step 5: Inspect the final diff** — confirm only the intended render function and supporting documentation changed.
