# mynyl — Claude Code Context

## What this is
A browser-based vinyl record audio player. Purely client-side HTML/CSS/JS — no build step, no framework, no dependencies except jsmediatags (CDN). Open `index.html` directly in a browser.

## File structure
```
index.html          — HTML shell only, no inline CSS or JS
css/
  main.css          — structural styles only (layout, sizing, no colors)
  theme-wood.css    — warm amber/mahogany color theme (swap to change theme)
js/
  audio.js          — Web Audio API: crackle synthesis, fade helpers, level meter
  render.js         — Canvas rendering: background, record, tonearm, geometry
  player.js         — State machine: playback, drag, keyboard, file loading
```

Script load order matters: `audio.js` → `render.js` → `player.js`. Each file depends on globals from the previous.

## Architecture

### Three layered canvases (z-index order)
- `#bg-canvas` — animated radial gradient background
- `#record-canvas` — spinning vinyl disc, grooves, label / album art
- `#arm-canvas` — tonearm, headshell, stylus, pivot bearing

### Key globals (set in render.js, used across files)
- `GEO` — tonearm geometry: `ARM_LEN`, `aOuter`, `aInner`, `aParked`
- `REC_R`, `REC_CX`, `REC_CY` — record radius and center
- `PIV_X`, `PIV_Y` — tonearm pivot position

### Audio chain
```
<audio> → sourceNode → analyser → musicFadeGain → masterGain → destination
crackleNode (ScriptProcessor) → crackleGain → masterGain
```

### Playback state machine (player.js)
States: idle → spinning up (2s crackle) → playing → song end (2s crackle) → idle
Key flags: `isPlaying`, `isSpinningUp`, `songIsOver`, `hasFile`, `isDragging`

## Theming
To add a new theme, duplicate `css/theme-wood.css`, rename it, update the color variables, and change the `<link>` in `index.html`. The theme file owns all colors; `main.css` owns all structure.

## Needle geometry
- `aOuter` — arm angle when stylus is at outer groove edge (~91% of record radius)
- `aInner` — arm angle when stylus is at inner boundary (30% of record radius, keeps needle clear of label)
- `aParked` — arm resting position to the right of the record
- `tipOnGroove(angle)` — returns true if tip is within the droppable groove zone

## Crackle timing
1. User drops arm on record → 2s crackle → music fades in
2. Song ends → music fades out + crackle fades in simultaneously → 2s crackle → fade out
3. Stop button kills crackle immediately and resets

## Notes for future work
- `window._labelImage` holds the decoded album art Image object (set by jsmediatags in player.js, read in render.js)
- `window._trackTitle` / `window._trackArtist` are set from filename parsing (format: `Title - Artist.mp3`)
- The ScriptProcessor API is deprecated but has no silent cross-browser alternative yet; keep an eye on AudioWorklet support
- Resize handler recomputes all geometry — no hardcoded pixel values anywhere
