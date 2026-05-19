# H5 Game Security Checklist

Use this reference for authorized H5 game, casino, slot, card, or betting game security reviews.

## Scope And Conduct

- Confirm test mode, staging, sandbox account, or explicit authorization before testing wagering, wallet, or settlement behavior.
- Do not brute force, bypass access controls, perform denial-of-service, or manipulate real funds.
- Report sensitive exposure by category and location. Do not paste full token, cookie, key, or personal data values.
- Keep adversarial checks bounded: one or a few low-rate requests, using test accounts and test balances only.

## Controlled Adversarial Checks

- Replay a single captured launch, bet, spin, or wallet-refresh request in the same test session and verify idempotency/replay rejection.
- Modify one low-risk parameter at a time, such as game id, round id, bet denomination, device type, language, or provider id; verify server-side validation.
- Duplicate a test account session in two tabs and check round/balance consistency.
- Double-click or rapidly tap a bet/spin button for a short manual burst; verify server-side duplicate prevention.
- Refresh, go back, or disconnect network during a test round; verify settlement and balance reconciliation.
- Copy a game launch URL to a fresh browser or logged-out session; verify token expiry and account binding.
- Expire or remove a token locally and verify WebSocket/API rejection without leaking debug details.

Do not scale these checks into stress testing, brute force, rate-limit bypass, or provider abuse.

## Client And URL Exposure

- Check whether game launch URLs contain long-lived `token`, `api_game_token`, `user_id`, balance, bet, provider, or debug parameters.
- Check whether a copied launch URL works in a fresh browser, another account, or after logout. This can indicate replay or bearer-token leakage.
- Check debug/test parameters such as `debug=true`, `test=true`, `skmode=true`, `mock`, `dev`, `env`, `uid`, or feature flags. Verify they are server-authorized, not just client-known.
- Check source maps and unminified bundles for exposed endpoints, keys, internal routes, test flags, or comments.
- Check localStorage/sessionStorage for bearer tokens, game tokens, user profile, balance, or provider credentials.

## Auth, Session, And Storage

- Cookies that carry auth should be `HttpOnly`, `Secure`, and have a deliberate `SameSite` policy.
- Bearer tokens should be short-lived, scoped, and rotated on logout/session refresh.
- Logout should invalidate game-launch tokens and active WebSocket/session state when applicable.
- Duplicate tabs and reloads should not create inconsistent session or wallet state.

## API And WebSocket Risks

- Verify every sensitive endpoint enforces server-side authentication and authorization.
- Verify bet amount, currency, game id, player id, and provider id are validated server-side.
- Verify game result, win amount, and balance settlement are never trusted from client-controlled data.
- Check replay protections for bet/spin/settlement requests: nonce, round id, idempotency key, timestamp, or server-side state machine.
- Check rate limits for login, launch-game, bet/spin, wallet refresh, and provider callbacks.
- For WebSockets, check authentication on connect and re-auth/reject behavior after token expiry or logout.
- Validate CORS: avoid wildcard origins with credentials; allow only expected origins.

## Frame, PostMessage, And Browser Policy

- Check whether games run in iframes and whether `frame-ancestors`/`X-Frame-Options` are intentional.
- Validate `postMessage` handlers check exact trusted origins and message schema.
- Check CSP for script-src, connect-src, frame-src, img-src, and media-src. Flag overly broad `*`, `unsafe-inline`, or untrusted third-party script sources when they affect account/game state.
- Check mixed content and insecure HTTP asset/API usage.

## Game Integrity And Abuse Cases

- Try UI-level edge cases first: double click, rapid spin, back/forward, refresh during spin, network drop/reconnect, duplicate tab, and concurrent launch.
- Confirm balance and round state reconcile after interrupted play.
- Confirm client-side disabled buttons are not the only protection against repeated wagering.
- Confirm all settlement-sensitive state has a server-side audit trail.

## Severity Calibration

- Critical:
  - Client can alter balance, result, win amount, or settlement.
  - Launch/bet tokens are reusable across accounts or after logout.
  - Pre-auth auth bypass, account takeover, or direct wallet impact.
- High:
  - Sensitive bearer tokens stored or exposed in URLs with long lifetime.
  - Missing server-side authorization for game or wallet endpoints.
  - Replayable bet/spin/settlement operations with financial impact.
  - Wildcard CORS with credentials on sensitive APIs.
- Medium:
  - Source maps expose internal endpoints or debug flags.
  - Weak CSP or unsafe postMessage patterns with plausible account impact.
  - Insufficient rate limiting on launch/game actions.
- Low:
  - Non-sensitive information disclosure.
  - Security headers missing without a direct exploit path.

## Evidence To Capture

- Tested endpoint or UI path.
- Reproduction steps with test account/environment.
- Request/response shape with sensitive values redacted.
- Impact in terms of account, wallet, game integrity, or data exposure.
- Suggested fix that names the enforcing component: gateway, backend API, game provider callback, wallet service, or frontend.
