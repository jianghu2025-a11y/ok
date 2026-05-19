# H5 Game Performance Checklist

Use this reference for H5 game lobby and runtime performance audits.

## Setup

- Record target URL, environment, account state, viewport, user agent, network condition, and cache state.
- Test at least:
  - Cold unauthenticated load when public access exists.
  - Authenticated lobby load when the user provides a session.
  - One representative game launch.
  - One mobile viewport around 390x844 or the target production device.
- Keep lobby metrics separate from in-game metrics.

## Core Metrics

- Navigation:
  - TTFB
  - DOMContentLoaded/load where available
  - LCP, CLS, INP or TBT where available
- Network:
  - Total requests
  - Encoded transfer size
  - Decoded JS/CSS size
  - Largest resources
  - API request count and slow endpoints
  - Cache-control, compression, and CDN hosts
- Runtime:
  - Console errors and unhandled promise rejections
  - Long tasks over 50 ms
  - JS heap before/after interaction loop
  - FPS or frame pacing for canvas/WebGL
  - Canvas/WebGL blank, clipping, resize, or input-mapping issues

## Severity Heuristics

Treat these as starting points; adjust for the target market and game type.

- Critical:
  - Game cannot load, canvas is blank, or primary interaction fails.
  - Mobile load crashes or repeatedly reloads.
  - Cold load exceeds 8 MB encoded transfer on mobile before first useful interaction.
  - Severe memory growth or FPS collapse during a short play loop.
- High:
  - More than 200 requests or more than 5 MB encoded transfer on lobby/game entry.
  - Initial JS/CSS decoded size over 2 MB without strong route splitting.
  - LCP over 4s or TBT over 600ms on a normal mobile profile.
  - Large PNG/GIF/TTF assets blocking first screen.
  - Background audio/video requested before user action.
- Medium:
  - Offscreen images, inactive skins, inactive tab icons, or game cards load eagerly.
  - Missing Brotli for JS/CSS when gzip is already used.
  - HTML lacks clear revalidation policy.
  - API config/list requests can be batched or cached.
- Low:
  - Minor cache header improvements for hashed static assets.
  - Non-critical console warnings without user impact.

## H5 Game Optimization Patterns

- Split lobby, provider list, account, promotion, and game-runtime code into route-level chunks.
- Avoid bundling full UI libraries when only a small subset is used.
- Lazy-load offscreen game cards and inactive category icons.
- Convert large PNG/GIF assets to WebP/AVIF where supported.
- Convert TTF fonts to WOFF2 and subset glyphs where practical.
- Do not preload background music or large game audio before user interaction.
- Preconnect to critical CDN/API/game-provider origins.
- Use texture atlases or spritesheets for runtime assets when the engine benefits from them.
- Cache hashed assets with long max-age and immutable; keep HTML on no-cache or short revalidation.
- For game engines, preload only the current scene and likely next scene, not every provider/game asset.

## Evidence To Capture

- Resource summary by type and host.
- Top 10 largest resources with size, type, cache header, and whether each is first-screen-critical.
- Slowest API/resource timings.
- Screenshots for lobby, game loading, first playable state, and any broken state.
- Console error list with count and representative message.
- Before/after memory or FPS notes when testing repeated play loops.
