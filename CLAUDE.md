# angeloalaimo

## Additional Context

Read `README.md` and `TODOS.md` at the project root for broader project context, current backlog, and planned work.

## Project Overview

Single-page static landing page for Angelo Alaimo. A centered rounded square divided into 4 colored quadrant buttons. Currently only the top-left (LinkedIn) quadrant is active; the other three are placeholder slots for future links.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Markup | HTML5 |
| Styling | CSS3 (inline) |
| JavaScript | Vanilla ES5 (inline, ~110 lines) |
| Build | None (static files) |

## Project Structure

```
angeloalaimo/
├── index.html          # Single-page landing (HTML + inline CSS + inline JS)
├── favicon.svg         # Vector favicon (modern browsers)
├── favicon.ico         # Multi-size ICO (16/32/48px, legacy browsers)
├── apple-touch-icon.png # 180x180 PNG (iOS home screen)
├── .gitignore
├── CLAUDE.md
├── README.md
├── TODOS.md
└── tmp/                # User-provided staging files (gitignored)
```

## Development Commands

```bash
open index.html                    # Open in default browser (macOS)
python3 -m http.server 8321       # Optional: serve locally for testing
# No build, lint, or test commands — static HTML
```

## Architecture / Key Patterns

### Layout

The page uses a 2x2 CSS Grid inside a `<div class="grid">` container. Centering is via flexbox on the `<body>`. Each quadrant applies its own `border-radius` to the appropriate outer corner (e.g., `.blue` gets `70% 0 0 0` for top-left). This avoids `overflow: hidden` on the grid container, which would clip hover `scale()` transforms.

**Why per-quadrant border-radius:** Using `overflow: hidden` on the container clips the `scale(1.05)` hover effect. Applying radius to individual corners keeps the rounded-square appearance without clipping.

**Why 70% radius:** The reference design is a 400pt square with 140pt corner radius. Each quadrant is half the grid (200px), so 70% of 200px = 140px, matching the reference exactly. Percentage-based values scale proportionally with the grid size.

### Grid Sizing

Width is `min(80vmin, 400px)` with `aspect-ratio: 1` and explicit `grid-template-rows: 1fr 1fr`. The `vmin` unit ensures the square stays proportional regardless of viewport aspect ratio. `aspect-ratio` guarantees the height matches width. `gap: 0` is explicit so quadrants butt up seamlessly.

### Design Tokens

| Token | Hex | Usage |
|-------|-----|-------|
| Blue | `#9AC5E8` | Top-left quadrant (LinkedIn) |
| Blue hover | `#7AADD0` | Top-left hover state |
| Green | `#A5D6A0` | Top-right and bottom-left quadrants (inactive) |
| Green hover | `#85B680` | Top-right/bottom-left hover (reserved) |
| Coral | `#E8908A` | Bottom-right quadrant (inactive) |
| Coral hover | `#D0706A` | Bottom-right hover (reserved) |
| Background | `#f0f0f0` | Page background |
| Label text | `#fff` | Quadrant labels (white) |
| Corner radius | `70%` | Outer corner radius on each quadrant |
| Font | Helvetica Neue | Primary font (thin weight 200 for labels) |

### Quadrant Elements

- The active LinkedIn quadrant is an `<a>` tag (semantic navigation). Future active quadrants should also use `<a>` when they get links.
- Inactive quadrants are `<div aria-hidden="true">`. When activating a quadrant: change it to `<a href="..." target="_blank" rel="noopener noreferrer" class="quadrant {color}" aria-label="{Label}">`, add a `<span class="label">{Label}</span>` inside, add hover/focus CSS matching the `.blue` pattern, and include `.{color}.auto-hover` compound selectors alongside `:hover` for auto-hover compatibility. The JS idle animation discovers active quadrants via `a.quadrant[href]`, so no JS changes are needed.
- Top-right and bottom-left share the same green color (`#A5D6A0`). They use separate classes (`.green-tr`, `.green-bl`) only because each needs a different `border-radius` corner.
- The hover effect uses `scale(1.05)` (not `translateY`), so it generalizes to any quadrant position.
- Label text is white, lowercase, Helvetica Neue thin (font-weight: 200). Visible only on hover when the background darkens — this maintains WCAG AA contrast.
- Labels are absolutely positioned within the quadrant, placed in the flat rectangular area opposite the curved corner. Each quadrant needs a different position:
  - Top-left (blue): `top: 60%; left: 54%` (text sits below-right of curve)
  - Top-right: `top: 60%; left: 46%` (text sits below-left of curve)
  - Bottom-left: `top: 40%; left: 54%` (text sits above-right of curve)
  - Bottom-right: `top: 40%; left: 46%` (text sits above-left of curve)

### Accessibility

- `focus-visible` outline (3px solid white, offset -6px) provides keyboard navigation visibility on the blue background.
- Inactive quadrants use `aria-hidden="true"` — they are purely decorative and don't confuse screen readers.
- The `<a>` tag has `aria-label="LinkedIn"` for screen reader context.
- `@media (prefers-reduced-motion: reduce)` suppresses `transform` and `transition` on hover/auto-hover states. The JS idle animation also checks `matchMedia` and disables itself entirely when reduced motion is preferred.

### Idle Auto-Hover Animation

An inline `<script>` IIFE (~110 lines) at the end of `<body>` draws attention to active links when the user is idle. No external dependencies.

**How it works:** After `IDLE_TIMEOUT` ms of no interaction, the script cycles through each active quadrant by adding an `.auto-hover` CSS class (which mirrors `:hover` via compound selectors). Each quadrant is highlighted for `HOLD_DURATION` ms, then the class moves to the next. After one full cycle, it pauses for `CYCLE_PAUSE` ms, then restarts.

**Timing constants** (at the top of the IIFE):

| Constant | Value | Purpose |
|----------|-------|---------|
| `IDLE_TIMEOUT` | 4000ms | Idle time before animation starts |
| `HOLD_DURATION` | 2000ms | Time each quadrant stays highlighted |
| `CYCLE_PAUSE` | 3000ms | Pause after completing one full cycle |

**Why IDLE_TIMEOUT > CYCLE_PAUSE:** Creates a perceptible difference between "pause between cycles" and "restart after user interaction."

**Active quadrant discovery:** `grid.querySelectorAll('a.quadrant[href]')`. Uses the existing convention that active = `<a>`, inactive = `<div>`. DOM order matches visual order (TL → TR → BL → BR). When a new quadrant is activated, it automatically joins the cycle — zero JS changes needed.

**Idle detection events:** `mousemove`, `pointerdown`, `keydown` on `document`; `focusin` on `.grid`. Any interaction immediately cancels the animation (removes `.auto-hover`, clears all timers) and restarts the idle timer. Both `.auto-hover` and `:hover` apply identical styles, so any momentary overlap during cancellation is invisible.

**CSS `.auto-hover` pattern:** Hover rules use compound selectors (e.g., `.blue:hover, .blue.auto-hover { ... }`). This keeps a single source of truth for hover visuals. When activating a new quadrant's hover CSS, always include the `.auto-hover` compound.

**Page Visibility:** Animation pauses when the tab is hidden and restarts the idle timer when visible (only if reduced motion is not active).

**Reduced motion:** Checks `matchMedia('(prefers-reduced-motion: reduce)')` at load time and listens for runtime changes. When active: no idle timer, no animation. CSS also suppresses transforms/transitions as defense-in-depth.

### Favicon

Three favicon formats cover all browsers:

| File | Size | Format | Purpose |
|------|------|--------|---------|
| `favicon.svg` | 64x64 viewBox | SVG | Modern browsers (scalable, preferred) |
| `favicon.ico` | 16/32/48px | ICO | Legacy browsers |
| `apple-touch-icon.png` | 180x180 | PNG | iOS home screen bookmarks |

The favicon reproduces the 2x2 grid layout using the exact design token colors (blue, green, green, coral) with 70% rounded corners on each outer edge and a transparent background. The SVG uses `<path>` elements with arc commands for the rounded corners. The ICO and PNG were generated from Pillow using `pieslice` + rectangles to reconstruct the same shape at raster sizes.

**Regenerating raster favicons:** If design token colors change, regenerate the ICO and PNG. The SVG can be edited by hand (just update `fill` attributes). For the raster files, use a Python script with Pillow — see git history for the generation approach.

## Deployment

Deployed via rsync to a chroot jail on ispeakofcake.com.

| Setting | Value |
|---------|-------|
| Host | ispeakofcake.com |
| Port | 4947 |
| User | angeloal_deploy |
| Web root | /web |
| Method | rsync over SSH |

```bash
rsync -avz -e "ssh -p 4947" index.html favicon.svg favicon.ico apple-touch-icon.png angeloal_deploy@ispeakofcake.com:/web/
```

## Commit Workflow Overrides

These fill the placeholder slots in the root commit workflow.

**Step 2 — Build and verify:** No build step. Verify by opening `index.html` in a browser and confirming the layout, colors, hover effect, and LinkedIn link work correctly.

**Step 10 — Deploy:** `rsync -avz -e "ssh -p 4947" index.html favicon.svg favicon.ico apple-touch-icon.png angeloal_deploy@ispeakofcake.com:/web/`
