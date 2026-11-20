# RN presidential succession implementation plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Enforce the existing two-consecutive-term limit while allowing the user's party to nominate and campaign for a successor.

**Architecture:** Derive eligibility from the existing `inaugurated` and `consecutiveTerms` fields. Rebuild the PASO roster without the outgoing president and mark the first eligible party figure as the player's succession candidate. Preserve player control for a forced successor ticket in the general election.

**Tech:** Single-file HTML/JavaScript, localStorage, Firecrawl isolated browser QA.

**Spec:** `docs/superpowers/specs/2026-11-20-rn-presidential-succession-design.md`

- [x] Reproduce the bug: a two-term incumbent appeared as a PASO candidate.
- [x] Add an eligibility-aware party candidate builder.
- [x] Rebuild term-limited PASO rosters during campaign creation and save normalization.
- [x] Explain succession in the PASO interface.
- [x] Keep the successor party ticket player-controlled in a forced general election.
- [x] Verify the incumbent is absent from the successor PASO.
- [x] Verify the successor ticket can enter the general election and win the presidency.
- [x] Validate JavaScript syntax and preview deployment readiness.
