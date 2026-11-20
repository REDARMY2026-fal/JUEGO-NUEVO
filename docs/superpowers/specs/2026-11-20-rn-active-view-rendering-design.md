# RN active-view rendering design

## Intent
Reduce unnecessary DOM work during campaign actions and quarterly updates while keeping the existing single-file GitHub Pages architecture and all gameplay behavior intact.

## Goals
- Keep the global date, treasury, debt, approval, employment, industry, stability, identity, and status indicators fresh after every state change.
- Render only the currently active screen after ordinary actions.
- Render a screen when the player navigates to it.
- Preserve election-countdown updates: election scrutiny functions may continue to call their dedicated render functions.
- Preserve save data, formulas, UI copy, and all existing mechanics.

## Non-goals
- No change to campaign/PASO action cadence or election balance.
- No change to save schema or localStorage keys.
- No dependencies, framework migration, or visual redesign.
- No merge to main or production deployment as part of this change.

## Approaches
1. **Full modular rewrite:** split the 170 KB single HTML file into modules. Rejected for this focused optimization because it expands the change surface and increases deployment risk.
2. **Active-view rendering:** keep the current helpers and dispatch only the active view from render(), while refreshing shared header metrics every time. Chosen because it removes hidden-screen DOM updates without changing the architecture.
3. **DOM caching/memoization:** cache individual markup fragments and skip unchanged nodes. Rejected for now because it adds invalidation complexity and can leave stale UI.

## Design decisions
- The active view identifiers remain: overview, economy, finance, industry, provinces, society, government, elections, history.
- The shared header is refreshed on every render.
- Each view's existing render logic is moved under its matching active-view branch.
- Navigation continues to call render() through setView().
- Election scrutiny retains its explicit renderElections() calls so live vote-count updates remain visible.
- The existing save format and all simulation calculations remain untouched.

## Safety constraints
- Work only on the existing Society pull-request branch.
- Keep main and the production deployment unchanged.
- Preserve all old-save migration behavior.
- Verify desktop/mobile layout, all nine views, policy actions, quarter close, save/reload, and old-save migration in an isolated browser.
