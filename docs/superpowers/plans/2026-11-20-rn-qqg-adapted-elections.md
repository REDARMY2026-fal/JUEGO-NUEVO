# Implementation plan — RN electoral system

1. Create an isolated branch from the current RN development branch.
2. Inspect and preserve the current election state shape and save migration.
3. Add a design specification before implementation.
4. Improve fictional opposition tickets and candidate metadata.
5. Add region/population-aware district scrutiny and runoff transfer logic.
6. Expand campaign action choices while retaining the existing quarterly action cap.
7. Improve election UI to show tickets, platforms, district reporting and results.
8. Run syntax checks, legacy-save tests, full election lifecycle tests, runoff tests, term-limit tests, mobile viewport checks and regression smoke tests across the main game views.
9. Deploy only a preview; keep production untouched and open a reviewable PR.
10. Prepare a separate short promotional-video concept for RN; estimate generation cost before spending any Higgsfield credits.
