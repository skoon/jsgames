# JS Mini-Games Style Guide

This style guide documents the shared visual language and CSS architecture defined in [common.css](file:///z:/www/jsgames/common.css). All games in this collection share a dark, tactile, monospace-first aesthetic with crisp hairline borders, subtle radial depth, and high-contrast semantic accents.

---

## 1. Design Tokens (`:root`)

All colors, fonts, radii, shadows, and layout offsets are exposed as CSS custom properties in `:root`. Individual games should **override tokens in their own `<style>` block** rather than duplicating base layout or component rules.

### Typography
| Token | Value / Purpose |
| :--- | :--- |
| `--font-mono` | `ui-monospace, "SF Mono", Menlo, Consolas, monospace` — default font for all `#stage` games and numeric readouts. |
| `--font-sans` | `"Inter", system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif` — used by wide-layout document/dashboard games (`body.layout-wide`). |

### Surfaces & Ink
| Token | Default | Purpose |
| :--- | :--- | :--- |
| `--bg-top` / `--bg-mid` / `--bg-bottom` | `#1a2c2b` / `#0a1415` / `#05090a` | Three-stop radial viewport background gradient. |
| `--stage-bg` | `#081014` | Inner background of `#stage`. |
| `--panel` | `rgba(9, 20, 21, 0.94)` | Default dark surface for cards and containers. |
| `--panel-top` / `--panel-bottom` | `rgba(20, 38, 41, 0.98)` / `rgba(5, 13, 16, 0.98)` | Vertical gradient stops for `.panel-card`. |
| `--topbar-bg` | `linear-gradient(...)` | Background for `.topbar` and `.hud-bar`. |
| `--ink` | `#f1f8f3` | Primary high-contrast text color. |
| `--muted` | `rgba(226, 240, 232, 0.5)` | Secondary labels, eyebrows, and subtitles. |
| `--faint` | `rgba(226, 240, 232, 0.24)` | Disabled states, dividers, and subtle hints. |
| `--line` | `rgba(150, 226, 203, 0.16)` | 1px hairline borders around bars, cards, and panels. |
| `--panel-border` | `rgba(255, 255, 255, 0.09)` | Subtle neutral border for wide-layout panels. |

### Semantic Accents
| Token | Default | Usage |
| :--- | :--- | :--- |
| `--teal` / `--teal-deep` | `#70e7c8` / `#2ec3a1` | Primary brand accent, focus rings, active highlights, primary score readout. |
| `--mint` / `--mint-deep` | `#86e7c5` / `#3ec49d` | Positive feedback, `.notice.good`, success states. |
| `--amber` / `--amber-deep` | `#ffc86b` / `#d99e38` | Warnings, `.brand-mark`, `.rank`, `.notice.warn`, highlight spans. |
| `--red` / `--coral` | `#ff8474` / `#ff9d87` | Errors, hazards, `.notice.bad`, danger states. |
| `--blue` | `#88caff` | Informational badges, secondary indicators. |
| `--violet` | `#c1a6ff` | Special items, rare states, bonus multipliers. |

### Warm Paper Surface (Document / Receipt Games)
Games that simulate physical paperwork (e.g., `pay-stub-sleuth.html`, `smart-cart.html`) pair the dark stage with warm paper cards:
- `--paper` (`#f6f1e4`) and `--paper-2` (`#ebe3d0`)
- `--paper-ink` (`#1b2224`), `--paper-muted` (`#5d6463`), `--paper-line` (`rgba(63, 57, 44, 0.2)`)

---

## 2. Layout Primitives

### Portrait Stage (`#stage`) — *Default for 15 of 16 games*
- **Viewport lock**: `body` uses `min-height: 100dvh`, `overflow: hidden`, `overscroll-behavior: none`, and `user-select: none` so touch/pointer gameplay feels native.
- **`#stage`**: Centered `3:4` aspect-ratio container (`width: min(100%, calc((100dvh - var(--stage-offset)) * 0.75))`), `border-radius: 20px`, `container-type: size`, and `touch-action: none`.
  - Override `--stage-offset` (default `56px`) if your bottom `.hint` or outer chrome needs slightly more/less vertical breathing room.
  - Because `container-type: size` is set on `#stage`, child components can use `cqw` units (e.g., `clamp(8px, 2cqw, 11px)`) to scale proportionally with the stage width.
- **`canvas`**: Fills `#stage` (`width: 100%; height: 100%`) with `touch-action: none`.
- **`#app`**: Full-bleed flex column inside `#stage` for DOM-based games.
- **`.hint`**: Single-line uppercase micro-caption centered below `#stage` for keyboard/mouse controls.

### Wide Layout (`body.layout-wide` + `.app-wide`)
For multi-column dashboard or shopping/simulation games that need more horizontal room (e.g., `smart-cart.html`):
- Add `class="layout-wide"` to `<body>` to enable vertical scrolling, `var(--font-sans)`, and normal text selection.
- Wrap content in `.app-wide` (`width: min(1160px, 100%)`).
- Use `.mono` on numbers, prices, and timers to enforce `var(--font-mono)` with `tabular-nums`.

---

## 3. Header, Brand & HUD

### `.topbar` & `.hud-bar`
- `.topbar`: Flex row with `space-between` alignment, `1px solid var(--line)`, `border-radius: 12px`, and `--topbar-bg`.
- `.hud`: Right-side flex group of `.stat` items (`gap: 11px`).
- `.hud-bar`: Standalone HUD bar for games without a `.topbar` wrapper.

### `.brand` & `.brand-mark`
- `.brand`: Left-aligned flex row containing `.brand-mark` (a `23x23px` rounded-square monogram badge in `--amber`) and a `<div>` with `<b>` (uppercase game title) and `<small>` (uppercase subtitle/tagline).

### `.stat`
- Right-aligned metric block with `<span>` (uppercase label in `--muted`) and `<strong>` (tabular-numeral value in `--ink`; the last `.stat` highlights in `--teal`).
- Add class `.flash` to `<strong>` to trigger `@keyframes stat-flash` when a score or counter updates.

---

## 4. Overlays, Modal Panels & Typography

### `.overlay` & `.panel`
- `.overlay`: Full-stage (`position: absolute; inset: 0; z-index: 10`) centered grid with `backdrop-filter: blur(5px)` and `fade-in` animation. Toggle visibility with the HTML `hidden` attribute (`.overlay[hidden] { display: none; }`).
- `.panel`: Scrollable inner container (`max-width: 340px`, `text-align: center`, `scrollbar-width: thin`).
- `.panel-card`: Optional bordered gradient card styling when the modal needs an explicit frame.

### Modal Typography & Rules
- `.eyebrow`: Small, wide-tracked uppercase category label above the title.
- `.title` / `.intro-title`: Large glowing game title (`clamp(22px, 6.2vw, 32px)`) in `--teal`. Wrap a word in `.accent` or `<span>` for `--amber` contrast.
- `.lead` / `.intro-lede`: Concise 1–2 sentence premise (`max-width: 34ch`).
- `.section-head`: Flex row with uppercase `<h2>` in `--muted`.
- `.rules` & `.rule`: Left-aligned grid of concise bulleted instructions with `--teal` dot markers (`::before`).

---

## 5. Buttons & Action Bars

- **`.btn`**: Pill-shaped (`border-radius: 999px`) primary CTA button with mint-teal gradient (`--btn-bg`), dark ink (`--btn-ink`), uppercase bold tracking, subtle bottom inset bevel, and hover/active press feedback.
- **`.btn-secondary`**: Ghost/outline pill button for secondary actions (e.g., Reset, Mute, Rules).
- **`.actionbar`**: Flex row (`space-between`) for footer controls inside `#app`.

---

## 6. Score, Summary & Feedback

- **`.final-score`**: Highlighted summary box on game-over overlays containing `.label` (or `<span>`) and `.score` (or `<strong>`).
- **`.rank`**: Uppercase performance tier or title in `--amber`.
- **`.best`**: High-score / personal-best line in `--muted`.
- **`.breakdown`**: Left-aligned grid for itemized end-of-round stats.
- **`.notice`**: Compact status text with semantic modifiers: `.notice.good` (`--mint`), `.notice.warn` (`--amber`), `.notice.bad` (`--red`).

---

## 7. Accessibility & Motion

- **Focus Rings**: All interactive elements (`button`, `input`, `select`, `textarea`, `[tabindex]`) receive a `2px solid var(--teal)` `:focus-visible` outline with `2px` offset.
- **Screen Readers**: Use `.sr-only` for live regions (`aria-live="polite"`) or hidden labels.
- **Reduced Motion**: `@media (prefers-reduced-motion: reduce)` disables all animations and transitions globally.

---

## 8. Template for New Games

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>New Game</title>
  <link rel="stylesheet" href="common.css" />
  <style>
    /* Optional per-game theme overrides */
    :root {
      --bg-top: #17233d;
      --bg-mid: #080b12;
      --bg-bottom: #05070b;
    }
    /* Game-specific styles only */
  </style>
</head>
<body>
  <div id="stage">
    <canvas id="game"></canvas>
    <div id="overlay" class="overlay">
      <div class="panel panel-card">
        <p class="eyebrow">Arcade</p>
        <h1 class="title">Game <span class="accent">Title</span></h1>
        <p class="lead">Short one-sentence description.</p>
        <button id="start" class="btn">Start</button>
      </div>
    </div>
  </div>
  <p class="hint">Click or press Space to play</p>
</body>
</html>
```
