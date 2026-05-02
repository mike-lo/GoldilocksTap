# Goldilocks Tap

A simple browser-based tool that empirically measures how reliably you can hit targets of different sizes on a touchscreen. Built to validate touch target dimensions through user testing rather than theory alone.

## Why

Designing for embedded touchscreens means working with physical constraints: pixel pitch, position accuracy (ΔE), false perspective, finger size, gloves. Specs give you theoretical minimums. Testing gives you actual numbers.

## What it does

**Configurable test setup:**
- Choose target sizes from 7 defaults (24, 32, 40, 48, 56, 64, 80px) or add your own
- Set hit-rate thresholds for the recommendations (default 90% / 95% / 98%)
- Capture tester variables: finger type and pad width, glove condition
- Auto-detects device type, viewport, DPR, and touch capability

**Test execution:**
- Targets appear one at a time in randomized order across 9 screen zones (corners, edges, center)
- Each size × zone combination is tested individually
- Works with mouse, touch, and pen input via Pointer Events API
- Visual feedback for each tap (hit/miss with miss-location marker)

**Results and analysis:**
- Recommended thresholds: minimum viable, sweet spot, plateau
- Hit-rate-vs-size curve with threshold reference lines
- 3×3 zone heatmap showing center vs edge vs corner accuracy
- Per-size breakdown table (hit rate, avg delta px, avg reaction time)
- Scatter plot of all tap locations
- Per-trial metrics: hit/miss, delta from center, reaction time, Fitts' Law throughput

**Export options:**
- JSON (full structured data)
- CSV (one row per trial, opens in Excel/Sheets)
- Standalone HTML report (self-contained, shareable)
- Print-friendly view (Ctrl+P → Save as PDF)

## Use

1. Open `index.html` in any browser, or visit the [live demo](#)
2. Configure your test (sizes, thresholds, tester info)
3. Tap targets as they appear — duration depends on configuration (~3-4 minutes for 63 trials with defaults)
4. Review results, export data

## Methodology notes

- Targets appear one at a time in randomized order (Fisher-Yates shuffle) to avoid pattern learning
- Each size is tested across 9 zones (3×3 grid) to surface false-perspective and outer-frame ΔE effects
- Reaction time is measured from target appearance to first tap
- Fitts' throughput uses tap-to-tap distance for realism (not synthetic target-to-target)
- Hit detection uses raw target boundaries — taps outside the visible target are misses, no forgiveness padding

## Built with

Single-file vanilla JS / HTML / CSS. No frameworks, no dependencies, no tracking, no analytics. All processing happens in the browser; no data leaves your device unless you export it manually.

Targets browsers with Pointer Events support (Chrome 55+, Firefox 59+, Safari 13+, all modern mobile browsers).

## Origin

Built as a follow-up to a UX case study on designing for embedded touchscreens in medical devices. The original project relied on theoretical calculations from screen specifications. This tool fills the gap with empirical validation — for that project, and for any future one where touch-target dimensions need to be checked against the actual hardware they'll run on.

## License

MIT.