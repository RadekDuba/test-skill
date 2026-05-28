# MapTiler — Agent Skill

Expert coding skill for building location-aware applications across the **full MapTiler platform**. It gives AI coding agents the context to generate correct, production-ready code using MapTiler Cloud APIs, the MapTiler SDK , mobile SDKs, and on-premise tools.

Built on the [Agent Skills](https://agentskills.io) open standard, so the same skill works across **Claude Code**, **Gemini CLI**, **Cursor**, **Windsurf**, and other compatible AI agents.

---

## What it does

A skill is on-demand expertise: the agent loads it only when your request matches the skill's description, then follows its instructions instead of guessing. When you ask for maps, geocoding, weather, elevation, vector tiles, or any geospatial feature, this skill makes the agent:

- Generate **platform-correct code** for web (MapTiler SDK JS, Leaflet, OpenLayers), frameworks (React, Svelte, Vue, Angular, Cesium, deck.gl), mobile (iOS Swift, Android Kotlin, Flutter, React Native), and on-premise (MapTiler Server, MapTiler Engine).
- Call the **right Cloud REST API** (geocoding, weather, static maps, elevation, tiles, coordinates, IP geolocation, admin/tilesets) with correct endpoints and auth patterns.
- Apply **modern v4 styles** automatically, upgrading deprecated `*-v2` / `*-v3` style IDs wherever they appear.
- Use **pinned, current versions** for every SDK, CDN script, and dependency, with no unresolved template placeholders.
- Cross-reference **vector tile schemas** (Planet v4, Buildings, Contours, Outdoor, Ocean, Landcover, Landform, Cadastre) before inventing `source-layer` names, paint expressions, or filter keys.
- Prefer the granular **Buildings tileset** over default building footprints for 3D extrusion, facade coloring, or per-feature interaction.
- Handle **framework lifecycle correctly** — WebGL cleanup, stale-closure prevention in React, isolated reactivity, ES module vs UMD CDN selection.

---

## How skills, plugins, and agents fit together

It helps to keep three layers separate, because the install steps below differ by layer:

- **The skill** is the portable content: a `SKILL.md` plus a `references/` folder. This is what every agent actually reads, and it's identical across tools.
- **The plugin** is a Claude Code–specific *wrapper* for distributing the skill through a marketplace. Other tools don't use it.
- **The agent** (Claude Code, Gemini CLI, Cursor, Windsurf…) is the tool that loads the skill from its own skills directory.

So there are two ways to install: through the Claude Code plugin marketplace (Claude Code only), or by copying the skill folder into any agent's skills directory (works everywhere).

What triggers a skill is the same in every tool: the `description` field in the `SKILL.md` frontmatter. The agent matches your request against that description to decide whether to activate the skill, so installation alone isn't enough — the skill also has to be *registered* and *triggered*.

---

## Installation

### Claude Code — as a plugin (recommended)

Add the marketplace, install the plugin, then reload so the skill registers:

```
/plugin marketplace add RadekDuba/test-skill
/plugin install maptiler@test-skill
/reload-plugins
```

Confirm it worked: run `/plugin`, open the installed `maptiler` plugin, and check that it lists **Skills: maptiler** with status **Enabled**. The skill is model-invoked — it activates automatically when you ask something in scope (for example, "add a MapTiler geocoding search box to my MapLibre map").

> Note: plugins from this marketplace are fetched over HTTPS. No SSH key is required for the public repo.

### Claude Code — as a standalone skill

If you'd rather skip the plugin and drop the skill in directly:

```bash
# User level — available across every project
cp -r maptiler ~/.claude/skills/

# Project level — this repo only
mkdir -p .claude/skills && cp -r maptiler .claude/skills/
```

```powershell
# Windows (PowerShell), user level
Copy-Item -Recurse maptiler "$env:USERPROFILE\.claude\skills\"
```

### Gemini CLI

Install straight from the repository, pointing at the skill's subfolder:

```bash
gemini skills install https://github.com/RadekDuba/test-skill.git --path skills/maptiler --consent
```

Or copy it in manually:

```bash
# Global (every project)
cp -r maptiler ~/.gemini/skills/

# Project (run /trust in the session afterward, then restart)
mkdir -p .gemini/skills && cp -r maptiler .gemini/skills/
```

### Cursor

Project-scoped only. Copy the skill folder into the project's skills directory:

```bash
mkdir -p .cursor/skills && cp -r maptiler .cursor/skills/
```

### Windsurf

Project-scoped, read by the Cascade agent. Copy the skill folder in:

```bash
mkdir -p .windsurf/skills && cp -r maptiler .windsurf/skills/
```

Claude Code–specific frontmatter fields (such as `allowed-tools`) are ignored by Windsurf without causing errors.

### Other agents

Any tool that supports the [Agent Skills standard](https://agentskills.io) can use this skill. Place the `maptiler` folder (containing `SKILL.md` and `references/`) wherever that agent looks for skill definitions.

---

## Repository layout

```
.claude-plugin/
  marketplace.json    — Claude Code marketplace manifest
  plugin.json         — Claude Code plugin manifest
skills/
  maptiler/
    SKILL.md          — Skill entry point (always loaded when triggered)
    references/
      INDEX.md                          — Curated catalog of high-value docs
      versions.md                       — Pinned versions of every library & dependency
      cloud-api-*.md                    — Cloud REST APIs (geocoding, weather, static, …)
      cloud-admin-api-*.md              — Admin API (tilesets, uploads, analytics, keys)
      sdk-js-*.md                       — MapTiler SDK JS core
      web-libraries-*.md                — React, Vue, Svelte, Angular, Leaflet, OL, Cesium, deck.gl
      mobile-sdks-*.md                  — iOS, Android, Flutter, React Native
      on-prem-*.md                      — Self-hosted Server & Tile Engine CLI
      map-resources-schemas-*.md        — Vector tile schemas (Planet v4, Buildings, …)
      map-resources-specifications-*.md — MapLibre style spec (layers, sources, expressions)
      examples-*.md                     — Runnable per-platform examples
      complex-dds-expressions-guide.md  — Data-driven styling deep dive
README.md             — This file
LICENSE               — MIT
```

> For the Claude Code plugin to expose the skill, `SKILL.md` must live at `skills/maptiler/SKILL.md` — not at the repository root, and not inside `.claude-plugin/`. Only the manifests go in `.claude-plugin/`.

---

## Prerequisites

A MapTiler API key from [cloud.maptiler.com](https://cloud.maptiler.com/account/keys/).

---

## Built-in guardrails

The skill enforces several non-obvious rules that prevent common LLM failure modes on this platform:

- **Zero variable leak** — never emits raw template placeholders; substitutes real values from `references/versions.md`.
- **Style modernization** — auto-upgrades deprecated v2/v3 style IDs (`streets-v2` → `streets-v4`, `dataviz-dark` → `dataviz-v4-dark`, …) across web, mobile, fallbacks, and service-worker caches.
- **ColorRamp hallucination guard** — blocks fabricated SDK methods such as `.getExpression()` or `.getCSSGradientDirection()`.
- **Buildings tileset priority** — prefers the granular `buildings` source (facade/roof color, height, entrances, labels, unique IDs) over basic footprints.
- **Schema verification** — refuses to invent `source-layer` IDs, feature property keys, or paint/layout fields without checking the schema docs.
- **Framework lifecycle correctness** — enforces `map.remove()` on unmount, blocks reactive state from re-creating the whole map, and prescribes refs over re-runs for event handlers.

---

## Links

- [MapTiler Cloud Console](https://cloud.maptiler.com/)
- [MapTiler SDK JS — Docs](https://docs.maptiler.com/sdk-js/) · [GitHub](https://github.com/maptiler/maptiler-sdk-js) · [NPM](https://www.npmjs.com/package/@maptiler/sdk)
- [MapTiler iOS SDK](https://docs.maptiler.com/mobile-sdk/ios/) · [Android SDK](https://docs.maptiler.com/mobile-sdk/android/)
- [MapLibre Style Specification](https://maplibre.org/maplibre-style-spec/)

---

## License

MIT — see [LICENSE](LICENSE).
