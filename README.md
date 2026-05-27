# MapTiler — Agent Skill

Expert coding skill for building location-aware applications across the **full MapTiler platform**. Helps AI agents generate correct, production-ready code using MapTiler Cloud APIs, the MapTiler/MapLibre SDK family, native mobile SDKs, and on-premise infrastructure.

Built on the [Agent Skills](https://agentskills.io) open standard — works with **Claude Code**, **Cursor**, **Windsurf**, **Goose**, and other compatible AI agents.

## What this skill does

When a user asks for maps, geocoding, weather, elevation, vector tiles, or any geospatial feature, the AI agent will:
- Generate platform-correct code for **web** (MapTiler SDK JS, MapLibre GL JS), **frameworks** (React, Svelte, Vue, Angular, Leaflet, OpenLayers, Cesium, deck.gl), **mobile** (iOS Swift, Android Kotlin, Flutter, React Native), and **on-premise** (MapTiler Server, Tile Engine)
- Call the right **MapTiler Cloud REST API** (geocoding, weather, static maps, elevation, tiles, coordinates, IP geolocation, admin/tilesets) with the correct endpoints and auth pattern
- Apply **modern v4 styles** automatically, upgrading deprecated `*-v2` / `*-v3` style IDs in any URL, mobile endpoint, fallback layer, or cache list
- Use **pinned, current versions** for every SDK, CDN script, and dependency from `references/versions.md` — never emits unresolved `{{...}}` template variables
- Cross-reference **vector tile schemas** (Planet v4, Buildings, Contours, Outdoor, Ocean, Landcover, Landform, Cadastre) before inventing `source-layer` names, paint expressions, or filter keys
- Prefer the granular **Buildings tileset** over default `base-v4` / `streets-v4` building footprints for any 3D extrusion, facade coloring, or per-feature interaction
- Handle **framework lifecycle correctly** — WebGL cleanup, stale-closure prevention in React, isolated reactivity, ES module vs UMD CDN selection

## Installation

### Claude Code

```bash
# User-level (available across every project)
cp -r maptiler ~/.claude/skills/

# Project-level (this repo only)
mkdir -p .claude/skills
cp -r maptiler .claude/skills/
```

```bash
# Windows (PowerShell)
Copy-Item -Recurse maptiler "$env:USERPROFILE\.claude\skills\"
```

Or — if distributed as a Claude Code plugin:

```
/plugin marketplace add <repo-url>
/plugin install maptiler
```

### Cursor

Copy `SKILL.md` and `references/` into `.cursor/rules/maptiler/`.

### Windsurf

Append the content of `SKILL.md` to your `.windsurfrules` file. Keep `references/` alongside so the agent can open them on demand.

### Other agents

Any agent supporting the [Agent Skills standard](https://agentskills.io) can use this skill. Place `SKILL.md` and `references/` where the agent looks for skill definitions.

## Contents

```
.claude-plugin/
  plugin.json                               — Claude Code plugin manifest
SKILL.md                                    — Skill entry point (~510 lines, always loaded)
README.md                                   — This file
LICENSE                                     — MIT
references/
  INDEX.md                                  — Curated catalog of high-value docs
  versions.md                               — Pinned versions of every MapTiler library & dep
  cloud-api-*.md                            — Cloud REST APIs (geocoding, weather, static, etc.)
  cloud-admin-api-*.md                      — Admin API (tilesets, uploads, analytics, keys)
  sdk-js-*.md                               — MapTiler SDK JS core
  web-libraries-*.md                        — React, Vue, Svelte, Angular, Leaflet, OL, Cesium, deck.gl
  mobile-sdks-*.md                          — iOS, Android, Flutter, React Native
  on-prem-*.md                              — Self-hosted Server & Tile Engine CLI
  map-resources-schemas-*.md                — Vector tile schemas (Planet v4, Buildings, Outdoor, …)
  map-resources-specifications-*.md         — MapLibre style spec (layers, sources, expressions)
  examples-*.md                             — Runnable per-platform examples (~390 files)
  complex-dds-expressions-guide.md          — Data-driven styling deep dive
```

## Prerequisites

Users need a MapTiler API key from [cloud.maptiler.com](https://cloud.maptiler.com/account/keys/).

## Built-in guardrails

The skill enforces several non-obvious rules that prevent common LLM failure modes on this platform:

- **Zero variable leak** — never emits raw `{{...}}` template placeholders; substitutes from `references/versions.md`
- **Style modernization** — auto-upgrades deprecated v2/v3 style IDs (`streets-v2` → `streets-v4`, `dataviz-dark` → `dataviz-v4-dark`, etc.) across web, mobile, fallbacks, and service-worker caches
- **ColorRamp hallucination guard** — blocks fabricated SDK methods like `.getExpression()` or `.getCSSGradientDirection()`
- **Buildings tileset priority** — prefers the granular `buildings` source (facade/roof color, height, entrances, labels, unique IDs) over basic footprints
- **Schema verification** — refuses to invent `source-layer` IDs, feature property keys, or paint/layout fields without cross-referencing the schema docs
- **Framework lifecycle correctness** — enforces `map.remove()` on unmount; blocks reactive state from triggering full map re-creation; prescribes refs over re-runs for event handlers

## Links

- [MapTiler Cloud Console](https://cloud.maptiler.com/)
- [MapTiler SDK JS Documentation](https://docs.maptiler.com/sdk-js/)
- [MapTiler SDK JS on GitHub](https://github.com/maptiler/maptiler-sdk-js)
- [MapTiler SDK JS on NPM](https://www.npmjs.com/package/@maptiler/sdk)
- [MapTiler iOS SDK](https://docs.maptiler.com/mobile-sdk/ios/)
- [MapTiler Android SDK](https://docs.maptiler.com/mobile-sdk/android/)
- [MapLibre Style Specification](https://maplibre.org/maplibre-style-spec/)

## License

MIT — see [LICENSE](LICENSE).
