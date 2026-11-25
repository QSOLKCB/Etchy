# Etch A Sketch — Single-File Web Toy

A classic Etch A Sketch toy as a single self-contained `.html` file.
No build step, no dependencies, no internet required — just open it in a browser and draw.

**File:** `etch-a-sketch.html` (~20 KB)

## Quick start

1. Download / save `etch-a-sketch.html`
2. Double-click to open in Chrome, Edge, Firefox, or Safari
3. Draw with keys, dials, or the on-screen pad
4. `Save PNG` to export your drawing

No server needed. It also works from `file://`.

## Features

- **Classic red toy frame** with shake animation to clear
- **Keyboard drawing** with smooth `requestAnimationFrame` loop
  - Classic 8-way: Arrows / WASD (hold two keys for diagonals) + single-key diagonals `Q` `E` `Z` `C`
  - Hex 6-way mode: `Q` `W` `E` / `A` `S` `D` mapped to 6 evenly-spaced 60° vectors
  - `Space` to shake-clear
- **Draggable dials** (mouse + touch)
  - Left dial = horizontal, right dial = vertical
  - Rotation is proportional to movement, with -180°/180° wrap handling
- **On-screen D-pads** (hold to draw — great for touch / mobile)
  - Classic 3×3 pad: 8 directions + `·` center button to re-center stylus
  - Hex pad: 6 directions, shown when Hex mode is selected
- **Color picker**
  - Native `<input type="color">` + 5 preset swatches
  - Changing color does not clear the drawing
- **Resizable canvas** (preserves drawing)
  - Small `400×300`, Medium `600×400` (default), Large `800×500`, XL `900×600`
  - Implemented via offscreen copy + `drawImage`, then clamp stylus position
- **Save PNG**
  - Exports via `canvas.toDataURL('image/png')` as `etch-a-sketch-WxH.png`
- **Responsive**: canvas scales with `max-width: 100%; height: auto`

## Controls cheat sheet

| Action | Input |
|---|---|
| Move | Arrow keys or WASD |
| Diagonals (Classic) | Hold 2 keys, or `Q` = ↖, `E` = ↗, `Z` = ↙, `C` = ↘ |
| Hex 6-way | Switch Move to `6-way Hex`, then `Q` `W` `E` `A` `S` `D` |
| Dials | Drag white knobs in circles |
| D-pad | Press-and-hold any arrow button |
| Re-center | `·` button in Classic pad |
| Shake clear | `Space`, `Shake to Clear` button |
| Color | Color well or swatch |
| Resize | Size dropdown |
| Export | Save PNG button |

## Project structure

```text
.
├── etch-a-sketch.html  # everything: HTML + CSS + JS inline
└── README.md           # this file
```

There are deliberately no external `.css` / `.js` files so the toy is portable for classrooms, demos, and offline use.

## How it works (code tour)

- `x, y` — stylus position, clamped to `[4, width-4]`
- `applyLineStyle()` — sets `lineWidth / lineCap / lineJoin / strokeStyle`
- `moveStylus(dx, dy)` — draws line segment, updates `x, y`, rotates dials
- `getMoveDelta()` — sums held keys + held D-pad buttons, normalizes with `Math.hypot` so diagonals aren't faster, multiplies by `speed = 2.2`
- `updateLoop()` — per-frame movement
- `setupDialDrag(el, axis)` — circular drag → `atan2` angle delta → `moveAmount = delta * 0.45`
- `resizeCanvas(w, h)` — offscreen backup, `canvas.width/height = …`, repaint bg, `drawImage(backup)`, reapply style
- `shakeScreen()` — CSS `.shaking` animation + 8-step fade with `rgba(197,201,196,0.35)` fills, then full clear + re-center
- `setColor(c)` / `setMoveMode(m)` / `saveDrawing()` — toolbar handlers

## Customization

Open `etch-a-sketch.html` and tweak:

- `const speed = 2.2;` — draw speed
- `const moveAmount = delta * 0.45;` — dial sensitivity
- `ctx.lineWidth = 2.5;` in `applyLineStyle()` — line thickness
- `BG_COLOR = '#c5c9c4'` — screen background
- Add swatches: copy a `<button class="swatch" …>` line
- Add sizes: add `<option value="WxH">` to `#sizeSelect` (limits are 200–1200 × 150–800 in `resizeCanvas`)

## Troubleshooting

- **Blank / script doesn't run?** Make sure you kept the full file including `<!DOCTYPE html>`, `<div id="toyFrame">`, and `</script></body></html>`. Early drafts were missing the `toyFrame` wrapper (causing `getElementById('toyFrame') → null`) and had stray `Use code with caution.` text inside the script (SyntaxError).
- **Space scrolls page?** The app calls `preventDefault()` for arrows + Space. If embedding elsewhere, keep that handler.
- **Resize clears drawing?** Only if you set `canvas.width` directly. Use the built-in `resizeCanvas()` which backs up first.
- **Save doesn't download?** Browsers block programmatic downloads on some `file://` + iframe setups. Open the file in a top-level tab and click again.

## Browser support

Modern evergreen browsers. Uses Canvas 2D, Pointer Events, `toDataURL`. No modules, no fetch, no WebGL.

## License

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
