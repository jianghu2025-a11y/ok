---
name: h5-game-perf-security-audit
description: Audit authorized H5/HTML5/browser games for performance, playability, and security risks. Use when Codex is asked to test H5游戏, web games, mobile browser games, canvas/WebGL games, casino/slot/betting games in test mode, loading speed, FPS, network payload, JS/CSS/image/font size, memory, console errors, login/game-launch flows, bet/spin/play actions, wallet/balance consistency, debug flags, source maps, API/WebSocket auth, token exposure, replay, CORS/CSP, or client-side tampering risks.
---

# H5 Game Performance & Security Audit

Use this skill to produce an evidence-backed test report for an H5 game or game lobby. Treat it as a QA + AppSec workflow with optional bounded adversarial validation for explicitly authorized test environments.

## Safety Boundary

- Test only systems the user owns or is authorized to test.
- Prefer test mode, staging, sandbox accounts, and test funds. Do not place real-money bets unless the user explicitly says the balance is test money and the action is allowed.
- Avoid destructive actions: no brute force, no credential stuffing, no payment abuse, no WAF bypass, no denial-of-service, no mass scanning.
- Do not extract, print, or persist secrets, session cookies, private keys, or personal data. Report exposure by location and risk, not by dumping values.
- If scope is unclear for authenticated, wagering, payment, or production behavior, ask for confirmation before acting.

## Authorized Adversarial Validation

When the user explicitly authorizes deeper testing on a test/staging environment, validate exploitability with controlled, low-rate probes that cannot harm accounts, funds, providers, or service availability.

- Prefer single-request or small-count proof checks over automated attacks.
- Use test accounts and test balances only.
- Redact tokens and personal data in all notes.
- Stop immediately if a probe changes real account state unexpectedly, triggers provider risk controls, or risks service degradation.
- Allowed validation examples: replay one captured test request, alter one non-sensitive parameter, duplicate-tab a play flow, double-click a spin/bet button, refresh during a round, reconnect WebSocket after logout, try a copied launch URL in a fresh session, inspect source maps for debug flags.
- Disallowed validation examples: high-volume scans, brute force, bypassing rate limits, credential attacks, WAF evasion, provider abuse, real-funds manipulation, or actions intended to degrade availability.

## Workflow

1. Define scope:
   - Target URL(s), environment, account type, test mode flag, and whether source code is available.
   - Allowed actions: login, launch game, deposit/test-credit, bet/spin/play, reconnect, logout.
   - Device profile: desktop, mobile viewport, slow network, target region.

2. Choose execution mode:
   - Use Chrome when the task depends on the user's logged-in session, cookies, extensions, or existing tab.
   - Use Playwright/browser automation for repeatable cold-load, warm-cache, mobile viewport, screenshot, and console/network checks.
   - Use repository inspection when source code is available; connect runtime findings to source files when possible.

3. Run the performance pass:
   - Measure cold load and warm cache.
   - Capture request count, transfer size, decoded JS/CSS size, image/audio/font payload, cache headers, TTFB, Web Vitals when available, long tasks, memory growth, FPS/canvas/WebGL stability, and console errors.
   - Read `references/performance-checklist.md` for the detailed checklist and severity thresholds.

4. Run the playability pass:
   - Verify lobby entry, category navigation, game launch, loading completion, first interaction, bet/spin/play action, result display, balance refresh, exit/back flow, and reconnect/reload behavior.
   - Capture screenshots or concise observations for failed or risky states.
   - For canvas/WebGL games, validate that the canvas is nonblank, correctly sized, interactive, and not visually clipped on mobile.

5. Run the security pass:
   - Focus on H5 game-specific risks: debug flags, game tokens in URLs, source maps, localStorage/sessionStorage tokens, client-controlled bet amount or balance, replayable game-launch URLs, WebSocket auth, weak CORS/CSP, mixed content, iframe/postMessage issues, and settlement authority.
   - Read `references/security-checklist.md` before making security claims.

6. Report findings:
   - Lead with the highest-impact issues and measured evidence.
   - Distinguish confirmed findings from likely optimizations and assumptions.
   - Include reproduction steps, impact, severity, and concrete fixes.
   - Use `references/report-template.md` when the user asks for a formal report.

## Measurement Guidance

- Prefer direct browser evidence over speculation. Use screenshots, network inventories, console logs, response headers, and reproducible timings.
- Run at least one mobile-sized viewport for H5 games unless the user says desktop-only.
- For authenticated sites, avoid logging out or changing user settings unless needed for the test.
- For betting/game-result flows, verify that the client only requests actions and displays outcomes; server APIs must authoritatively validate wager, balance, result, and settlement.
- For performance, separate "homepage/lobby" findings from "specific game runtime" findings because their bottlenecks are often different.

## Output Shape

For quick checks, return:

1. Overall status
2. Key measurements
3. Confirmed issues
4. Optimization opportunities
5. Security risks
6. Next tests recommended

For formal audits, use the report template and include an appendix of tested URLs, accounts/environment notes, devices, and limitations.
