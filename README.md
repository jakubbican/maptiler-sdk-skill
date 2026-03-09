# MapTiler SDK Expert Skill

An AI agent skill for building geospatial web apps with [MapTiler SDK JS v3](https://docs.maptiler.com/sdk-js/) — a premium ecosystem built on MapLibre GL JS that combines a powerful open-source rendering engine with ready-made map styles, cloud services, geocoding, and data visualization helpers in one cohesive package.

## What's inside

```
SKILL.md                        — Main skill file (16 sections)
references/
  helpers-api.md                — Complete helpers API reference
  cdn-versions.md               — CDN URLs, version history
  patterns-gotchas.md           — Common patterns & pitfalls
  map-styles.md                 — MapStyle enum + Language enum
  events.md                     — Map events reference
scripts/
  basic-map.html                — Minimal map setup
  interactive-layers.html       — Layers, toggles, fly to, style switch
  3d-terrain.html               — 3D terrain + exaggeration
  globe-projection.html         — Globe + halo/space (v3)
  clustering.html               — Point clustering (manual approach)
  helpers-dataviz.html          — Polyline, polygon, points via helpers
  geocoding-search.html         — Forward/reverse geocoding + fly to
  heatmap.html                  — Heatmap via helpers.addHeatmap()
```

## Key topics covered

| Topic | Where |
|-------|-------|
| `config` object (apiKey, language, session, units...) | SKILL.md §1 |
| MapStyle enum — all variants | SKILL.md §2 + references/map-styles.md |
| **Helpers** — addPolyline, addPolygon, addPoint, addHeatmap, takeScreenshot | SKILL.md §3 + references/helpers-api.md |
| Manual addSource / addLayer | SKILL.md §4 |
| **Geocoding** — forward, reverse, batch, GeocodingControl | SKILL.md §5 |
| Built-in API services (elevation, geolocation, math, coordinates...) | SKILL.md §6 |
| Camera — flyTo, fitBounds, centerOnIpPoint | SKILL.md §7 |
| Events reference | SKILL.md §8 + references/events.md |
| 3D terrain + Globe (halo/space, setProjection) | SKILL.md §9 |
| Framework integration (React, Vue, Next.js SSR) | SKILL.md §11 |
| **SDK & MapLibre relationship** — why consistent imports matter | SKILL.md §12 |
| Advanced modules (Weather, 3D, Elevation Profile, AR...) | SKILL.md §13 |
| Common pitfalls (14 items) | SKILL.md §14 |
| TypeScript types | SKILL.md §15 |
| Online documentation links | SKILL.md §16 |

## Core principles

1. **One ecosystem** — the SDK builds on MapLibre GL JS and adds map styles, cloud services, geocoding, helpers, and session management; use `@maptiler/sdk` consistently to keep it all working together
2. **Session billing** — `config.apiKey` mandatory, never hardcode in style URLs
3. **Type safety** — `MapStyle.STREETS` not `'https://api.maptiler.com/...'`
4. **Helpers or manual** — helpers for quick work, manual for full control (both valid)
5. **Globe native** — `halo: true, space: true` in constructor (v3.5+)
6. **Cleanup** — always `map.remove()` to prevent memory leaks

## Usage

### Claude Code

```bash
claude skill install https://github.com/jakubbican/maptiler-sdk-skill
```

### Gemini CLI

Add to your project's `GEMINI.md`:

```bash
curl -s https://raw.githubusercontent.com/jakubbican/maptiler-sdk-skill/main/SKILL.md >> GEMINI.md
```

### Cursor

Add to your project's `.cursorrules`:

```bash
curl -s https://raw.githubusercontent.com/jakubbican/maptiler-sdk-skill/main/SKILL.md >> .cursorrules
```

Or create `.cursor/rules/maptiler-sdk.mdc` for the newer rules format.

### Codex CLI

Add to your project's `AGENTS.md`:

```bash
curl -s https://raw.githubusercontent.com/jakubbican/maptiler-sdk-skill/main/SKILL.md >> AGENTS.md
```

### Any agent / system prompt

Reference `SKILL.md` directly from the raw GitHub URL, or copy its content into your agent's system prompt / context file.

## Version

Targets **MapTiler SDK JS v3.11.1** (MapLibre GL JS 5.x underneath).
CDN examples use `latest` alias — always loads the current release.

## Resources

- [MapTiler SDK JS Docs](https://docs.maptiler.com/sdk-js/)
- [API Reference](https://docs.maptiler.com/sdk-js/api/map/)
- [Helpers API](https://docs.maptiler.com/sdk-js/api/helpers/)
- [Client JS (API services)](https://docs.maptiler.com/client-js/)
- [MapTiler Cloud](https://cloud.maptiler.com)
- [GitHub — maptiler-sdk-js](https://github.com/maptiler/maptiler-sdk-js)
