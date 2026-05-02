# CLAUDE.md — Touch Target Accuracy Test

## Project purpose

An empirical tool that measures touch accuracy across different button sizes and screen zones. It validates touch target dimensions through real testing rather than theoretical calculations from screen specs. Part of a UX portfolio at mlewczuk.com, linked from a case study about designing embedded touchscreens for a medical DNA extraction instrument (4.5 years as lead UX designer). Intended audience: UX/hardware teams who need data to justify minimum tap target sizes.

## Technical constraints

- **Single file**: everything lives in `index.html` — HTML, CSS, JS. No exceptions.
- **No build step, no dependencies, no package.json**. Copy the file and it runs.
- **Deployable to GitHub Pages as-is.**
- **No external resources**: no CDN fonts, no analytics, no tracking, no network calls.
- **Pointer Events API** — works with mouse, touch, and pen without branching.

## Design principles

- **Lab instrument, not an app**: monochrome (`#1a1a1a` / white), minimal color (semantic only: amber warning, green pass, indigo plateau, blue run-B).
- **System fonts**: `-apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif`.
- **Section headers**: `0.75rem`, uppercase, `#888`, `letter-spacing: 0.08em` — used consistently across all cards.
- **No comments in code** unless the why is non-obvious. Well-named functions over explanatory prose.
- Keep additions minimal. Don't add error handling, fallbacks, or features beyond what's asked.

## File structure (`index.html`, ~1135 lines)

```
<style>          — all CSS (~200 lines)
<body>
  #setup-screen  — tester info, finger, conditions, sizes, thresholds, mode toggle
  #test-screen   — progress header + test area canvas
  #between-screen — interstitial between compare runs (hit rate summary + continue/cancel)
  #results-screen — single-run: verdict, curve, zone grid, per-size table, scatter, exports
  #compare-screen — two-run: overlaid curve, side-by-side zone grids, merged diff table, exports
<script>         — all JS (~700 lines)
```

## Key JS landmarks

| Symbol | Purpose |
|---|---|
| `DEFAULT_SIZES`, `ZONES`, `TRIALS_PER_COMBO` | Config constants at top of script |
| `state` | Single object: tester, trials, currentTrialIndex, currentTarget, timers, selectedSizes, thresholds |
| `compareMode`, `sessions[]` | Compare-mode flag + array of two session snapshots |
| `showScreen(id)` | Single entry point for all screen transitions |
| `updateStartBtn()` | Keeps start button label in sync with compare/session state |
| `snapshotSession()` | Captures `state` into a plain object after each run |
| `resetTestState()` | Centralised reset — clears trials, DOM targets, pendingFirstTap, acceptingTaps |
| `generateTrials()` | Builds shuffled trial list (size × zone × TRIALS_PER_COMBO) |
| `handlePointerDown → nextTrial → recordTap → finishTest` | Core test loop |
| `computeAndRenderResults()` | Single-run results: reads live `state` directly |
| `computeZoneStats(trials)` | Returns per-zone hit stats; used by both single and compare views |
| `fillZoneGrid(zonesByName, el)` | Renders colour-coded 3×3 zone grid into any element |
| `renderComparison(sA, sB)` | Orchestrates all compare-screen rendering from two snapshots |
| `renderComparisonCurve(...)` | Overlaid SVG curve, black (A) vs blue `#3b82f6` (B) |
| `renderComparisonTable(...)` | Merged size table with Δpp column |
| `computeSummary()` | Used by JSON export; does not mutate `state.selectedSizes` |
| `download(filename, content, mime)` | Blob download helper |

## Screens and state transitions

```
setup ──[start-btn]──► test ──[finishTest]──► results        (single mode)
setup ──[start-btn]──► test ──[finishTest]──► between        (compare, run 1)
between ──[continue]──► setup ──[start-btn]──► test ──[finishTest]──► compare  (run 2)
results/compare ──[restart]──► setup        (resets sessions, compareMode, testState)
between ──[cancel]──► setup                 (resets sessions, compareMode, testState)
```

## SVG rendering convention

All three SVG charts (`renderCurve`, `renderComparisonCurve`, `renderScatter`) build an HTML string and assign to `svg.innerHTML`. Inputs are always numbers — no user-supplied strings enter the SVG — so this is safe.

## What's been built

- [x] Core flow: setup → test → results
- [x] 9-zone grid (3×3: corners, edges, center), configurable sizes, thresholds
- [x] Metrics: hit/miss, delta px, reaction time, Fitts' Law throughput
- [x] Exports: JSON, CSV, standalone HTML report, print/PDF
- [x] **Compare two configurations mode**: run A → between-screen → run B → side-by-side compare screen with overlaid curve, zone grids, merged diff table, comparison exports

## Known non-issues (intentional choices)

- `pendingFirstTap` / `acceptingTaps` are module-level (not in `state`) — they govern the live test loop and are reset by `resetTestState()`.
- Results screen is not cleared on restart — it's fully re-rendered on the next run.
- `state.selectedSizes` persists across restarts so the user doesn't re-select sizes.
- Compare mode uses run A's thresholds for the overlaid chart threshold lines (consistent baseline).
