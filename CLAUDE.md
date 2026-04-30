# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Single-file static website (`Website.html`) — no build step, no dependencies to install. Open it directly in a browser or serve it with any static file server:

```bash
# Quick local server (Python)
python3 -m http.server 8080
# Then open http://localhost:8080/Website.html
```

To deploy, just push to `main`; the file can be served from any static host.

## Architecture

Everything lives in `Website.html` in three sequential sections:

1. **`<style>` block** — all CSS. Dark theme using Tailwind-inspired slate palette (`#0f172a`, `#1e293b`, `#334155`). Accent colour is sky blue (`#38bdf8`). The `.avatar` component handles both photo (`<img>`) and initials fallback automatically.

2. **HTML body** — four visible regions in order: `<header>`, `.filters` (sticky), `#map` (Leaflet), `.lab-list` (mobile card list, hidden on desktop). A `#detailModal` overlay sits outside the flow.

3. **`<script>` block** — no framework. Key globals:
   - `researchers` array — the single source of truth for all researcher data. Each entry has `id`, `name`, `photo`, `institution`, `location`, `coords`, `topic`, `focus[]`, `description`, `notable`, `email`, `phone`, `labWebsite`, `scholar`, `appearances[]`, and optionally `extraLinks[]`.
   - `markers` object — maps `researcher.id → Leaflet marker`.
   - `activeFilter` string — currently selected focus tag.
   - Core functions: `initMarkers()`, `initResearcherList()`, `applyFilter(filter)`, `openModal(researcherId)`, `closeModal()`.

## Adding or editing researchers

All content changes happen in the `researchers` array in the `<script>` block. Fields:
- `photo`: URL to a portrait image. Empty string `""` renders a coloured initials circle instead.
- `focus`: array of tag strings — must match the `data-filter` values on the filter buttons (`"BCI"`, `"Biohybrid"`, `"Neurostimulation"`, `"Wireless"`, `"Materials"`, `"Clinical"`).
- `appearances`: array of `{ label, url }` objects — shown as a "Talks & Appearances" section in the modal.
- `extraLinks`: optional array of `{ label, url }` — shown under "Links" in the modal.

To add a new filter category, add a `<button class="filter-btn" data-filter="NewTag">` in the HTML and use the same string in the researcher's `focus` array.

## Map configuration

The map is Leaflet 1.9.4 with CARTO dark tiles. Key behaviours enforced in the `L.map(…)` call:
- World bounds are hard-locked (`maxBoundsViscosity: 1.0`) — users cannot pan off the world.
- `minZoom` is computed dynamically by `getMinZoom()` so the world always fills the viewport; it is recalculated on `window resize`.
- `noWrap: true` on the tile layer prevents duplicate world copies.

## Git / GitHub

- Remote: `https://github.com/CharlyKerken/implant-radar`
- Branch: `main`
- Commit and push after every meaningful change. Use concise imperative commit messages (`Add photo for X`, `Fix filter bug`, `Update researcher description`).
