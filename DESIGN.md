# Durish Labs design spec

The shared look of the Durish Labs sites: this portfolio (durishlabs.com), the Character
Planner (planner.durishlabs.com) and the Forever API Atlas (atlas.durishlabs.com). It is
derived from this portfolio's `public/styles.css`, and this file is the source the other
two follow.

Each site stays self-contained: no shared CDN, package or cross-site stylesheet. Each copies
the token block below into its own CSS and keeps it in step with this file by hand. When a
token changes here, open an issue in each site's repo to follow.

## Decisions

Made 2026-11-20 after an audit of the three live sites (roadmap #14).

- **Dark only.** Every site sets `color-scheme: dark`. A light theme would be a new
  decision here first, with its own contrast checks.
- **One accent: lime `#d6ef8d`** on all three sites. Gold and amber survive only as the
  warning status colour, never as a link or button colour.
- **Fonts: system only.** `system-ui` for text, Georgia for display headings, the system
  monospace stack for code and API names. No web fonts, so no font files, no CDN and no
  CSP change. The Atlas drops its bundled DM Sans and IBM Plex Mono.
- **One masthead:** the square "DL" mark links to durishlabs.com on every site, followed by
  the site's own name. The Atlas's F∞ mark is retired.

## Tokens

Copy this block as it is. Sites may add their own tokens (the Planner's item-quality
colours) but don't redefine these.

```css
:root {
  color-scheme: dark;

  /* Surfaces */
  --background: #141715;    /* page */
  --panel: #1e2420;         /* cards, panels, table headers, dialogs */
  --sunken: #0f1210;        /* code blocks, inputs, detail panes */
  --accent-soft: #2a3320;   /* selected nav item, pressed toggle, selected row */

  /* Text */
  --ink: #f3f4e9;           /* body text, headings */
  --muted: #b9c4b9;         /* secondary text, labels, table headers */

  /* Accent */
  --accent: #d6ef8d;        /* links, primary buttons, focus ring, current item */
  --accent-hover: #e7f7b4;  /* primary button hover */
  --accent-ink: #17200f;    /* text on --accent */

  /* Lines */
  --line: #38443c;          /* decorative rules and panel edges only */
  --control-line: #6f7d72;  /* the edge of anything you can operate: inputs, outlined buttons */

  /* Status: always with a word or icon, never colour alone */
  --ok: #86cfa6;
  --warn: #e8b04f;
  --error: #f08f7e;
  --info: #8fb8ea;
  --other: #c3a8e6;

  /* Type */
  --font-text: system-ui, sans-serif;
  --font-display: Georgia, "Times New Roman", serif;
  --font-mono: ui-monospace, "Cascadia Code", "SF Mono", Menlo, Consolas, monospace;

  --text-sm: 0.875rem;      /* the floor: labels, tags, captions, meta. Never running text */
  --text-md: 1rem;          /* body, table cells, inputs, buttons */
  --text-lg: 1.15rem;       /* lead paragraphs; h3 in panel-heavy apps */
  --text-xl: 1.35rem;       /* h3; h2 in panel-heavy apps */
  --text-2xl: 1.75rem;      /* h2 */
  --display-sm: clamp(1.9rem, 1.2rem + 3vw, 3.4rem);  /* app page titles, featured headings */
  --display-md: clamp(3rem, 1.5rem + 6vw, 6.5rem);    /* article titles */
  --display-lg: clamp(3rem, 1rem + 8vw, 7.5rem);      /* the portfolio hero */

  /* Space */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-5: 1.5rem;
  --space-6: 2rem;
  --space-7: 3rem;
  --space-section: clamp(2.5rem, 1.5rem + 4vw, 4.5rem);

  /* Shape */
  --radius-sm: 0.25rem;     /* buttons, inputs, tags */
  --radius-md: 0.55rem;     /* panels, cards, dialogs, table wrappers */
  --wrap: 72rem;            /* reading pages */
  --wrap-wide: 100rem;      /* app workspaces */

  font-family: var(--font-text);
  background: var(--background);
  color: var(--ink);
  accent-color: var(--accent);
  scrollbar-color: var(--control-line) var(--background);
}
```

### Contrast

Checked with WCAG 2 relative luminance. Every text token passes 4.5:1 on every surface;
`--control-line` passes the 3:1 non-text minimum on every surface. `--line` (1.3–1.9:1) is
decorative: never let it be the only edge of a control.

| Text | `--background` | `--panel` | `--sunken` | `--accent-soft` |
|---|---|---|---|---|
| `--ink` | 16.3 | 14.3 | 17.0 | 11.9 |
| `--muted` | 10.0 | 8.8 | 10.5 | 7.3 |
| `--accent` | 14.3 | 12.5 | 14.9 | 10.4 |
| `--ok` | 9.9 | 8.7 | 10.3 | 7.2 |
| `--warn` | 9.3 | 8.1 | 9.7 | 6.8 |
| `--error` | 7.7 | 6.7 | 8.0 | 5.6 |
| `--info` | 8.8 | 7.7 | 9.2 | 6.4 |
| `--other` | 8.7 | 7.6 | 9.0 | 6.3 |
| `--control-line` (3:1) | 4.2 | 3.7 | 4.4 | 3.1 |

`--accent-ink` on `--accent` is 13.3:1, and 14.7:1 on `--accent-hover`.

### Status colours by site

The meaning stays each site's own; only the colour comes from here.

| Token | Planner | Atlas |
|---|---|---|
| `--ok` | `--ok` (synced, upgrade score, owned) | state *value*; kind *function* |
| `--warn` | `--warn` (stale, attention) | state *restricted*; kind *value*; the partial-snapshot notice |
| `--error` | `--error` (failed sync, errors) | states *unavailable*, *error*, *partial* |
| `--info` | | kind *table* |
| `--other` | | kind *other* |
| `--accent` | was gold `--accent`: links, primary buttons, current tab | was gold and green: links, current nav, pressed filters |

The Planner keeps its WoW item-quality colours (`--q-poor` to `--q-legendary`) as its own
tokens, unchanged. On the shared surfaces they pass 4.5:1 on `--background`, `--panel` and
`--sunken` (rare 5.1, epic 4.9 on `--panel`), but rare and epic fall to about 4.0–4.2:1
on `--accent-soft`: don't show item names on a selected-row fill.

## Type

- **Text** is `--font-text` at `--text-md` with `line-height: 1.6`. Dense UI (table cells,
  list rows, tags) may use 1.4–1.45. Line-height is always unitless.
- **Display** headings (`h1`, featured `h3`) use `--font-display`, weight 400,
  `line-height` about 1–1.05 and `letter-spacing` -0.04em to -0.065em, with
  `text-wrap: balance`. Section headings (`h2`, `h3`) stay in `--font-text`, bold.
- **Heading sizes:** on a page, `h2` is `--text-2xl` and `h3` `--text-xl`. Where most
  headings sit inside panels (an app whose tabs and dialogs are all cards, as in the
  Planner), they step down one: `h2` is `--text-xl` and `h3` `--text-lg`. Pick one per site
  and use it throughout.
- **Monospace** is for code, API names, paths and the small category labels only, never
  for prose or numbers in general. Set `font-size-adjust: from-font` on `body` so inline
  code matches the text's x-height, and size code blocks in `em` so they follow their
  context.
- **Sizes are `rem`**, never `px`, so a reader's browser font size works. Fluid sizes use
  `clamp()` with `rem` at both ends and `rem + vw` in the middle, and the maximum is at most
  2.5 times the minimum, so zoom can still reach 200%.
- **Minimums:** nothing under `--text-sm` (0.875rem, 14px), anywhere. Running text,
  table cells, inputs and buttons are at least `--text-md`. If a dense view doesn't fit,
  change the layout (wrap, stack, scroll a region), not the type size.
- Paragraphs cap at about 65ch. Use `text-wrap: pretty` on long-form paragraphs.
- No all-caps letter-spaced labels; the category label below replaces them.

## Layout and spacing

- Spacing comes from `--space-*`, set with `gap` on flex and grid parents rather than
  margins between siblings.
- Reading pages sit in `.wrap`: `width: min(var(--wrap), calc(100% - 2.5rem))`, centred.
  App workspaces may use `--wrap-wide`.
- Phone width (375–390px): no sideways page scroll, a side gutter of at least 1rem, rows
  that wrap or stack. Only tables and code may scroll sideways, inside their own region.
- No fixed heights on anything holding text; use `min-block-size` when a minimum matters.

## Components

### Masthead and brand

```html
<header class="masthead">
  <a class="brand" href="https://durishlabs.com/" aria-label="DL Durish Labs home">
    <span class="mark" aria-hidden="true">DL</span>
    <span class="brand-name">Durish Labs</span>
  </a>
  <a class="site-name" href="/">Character Planner</a>
  <nav aria-label="Main navigation">…</nav>
</header>
```

- Flex row, wraps, `gap: var(--space-4)`, `padding-block: 1.3rem`, a `--line` rule
  underneath. Height comes from the content.
- `.mark`: a 2.4rem square, `--accent` fill, `--accent-ink` text, `700 0.9rem`
  `--font-mono`, no radius.
- On the portfolio the brand links to its own home and there is no `.site-name`. On the
  other sites the mark and "Durish Labs" link to durishlabs.com, and `.site-name` (ink, 700)
  links to the site's own home.
- At phone width `.brand-name` may be visually hidden (keep it for screen readers), leaving
  the mark and the site name.
- Nav links: `--ink`, no underline, underlined on hover; the current page has
  `aria-current="page"` and shows a 2px `--accent` underline.
- App controls (the Planner's character switcher and Sync, the Atlas's Export) sit in the
  nav slot or in a toolbar below the masthead, not in a second brand bar.

### Links

`--accent`, underlined, `text-underline-offset: 0.22em`; on hover
`text-decoration-thickness: 0.14em`. Links inside running text are always underlined.
Navigation, breadcrumbs and row links may drop the underline only where their position
makes them obviously links, and must still underline on hover.

### Buttons

| Kind | Look |
|---|---|
| Primary | `--accent` fill, `--accent-ink` text, 700, `--radius-sm`, padding `0.65rem 1rem`; hover `--accent-hover` |
| Secondary | transparent, `--ink` text, 1px `--control-line` border; hover `--panel` fill and `--muted` border |
| Toggle / filter | as secondary; `aria-pressed="true"` gives `--accent-soft` fill, `--accent` border and text |
| Danger | as secondary with `--error` text and border |
| Disabled | `opacity: 0.5`, `cursor: not-allowed`; still `--text-md` |

Buttons are `--text-md`, at least 2.75rem tall on touch (`@media (pointer: coarse)`), and
use `<button>` for actions and `<a>` for navigation. A button-looking link uses the same
classes.

### Forms

Inputs and selects: `--sunken` fill, `--ink` text, 1px `--control-line` border,
`--radius-sm`, padding `0.6rem 0.75rem`, `font: inherit` at `--text-md` (which also stops
phones zooming on focus). Labels sit above in `--muted` at `--text-sm`, weight 600. Show
errors with `:user-invalid`, an `--error` border and a message in words.

### Panels and cards

`--panel` fill, 1px `--line` border, `--radius-md`, padding `--space-5` (`clamp(1.25rem,
4vw, 3rem)` for the portfolio's featured cards). Code blocks, detail panes and inputs use
`--sunken`. No shadows, gradients or coloured edge bars on cards; one radius per kind.

### Category label and tags

- **Category label** (the eyebrow over a heading): `--font-mono`, `--text-sm`,
  `--accent`, normal case.
- **Tag / badge / state chip:** `--text-sm`, 1px border, `--radius-sm`, padding
  `0.1rem 0.45rem`. Text in the status colour; border
  `color-mix(in oklab, currentColor 45%, transparent)`. The text says the state.

### Tables

- Semantic `<table>` with `<caption>`, `<thead>`, `scope` on header cells.
- Inside a scroll region when it can outgrow its space: `<div class="table-scroll"
  role="region" aria-labelledby="<caption id>" tabindex="0">`, `overflow: auto`,
  `container-type: inline-size`, sticky `thead th` on `--panel`.
- Cells `--text-md`, padding `0.75rem 1rem`, 1px `--line` rule between rows, aligned to
  the top. Header cells `--muted`, `--text-sm`, weight 600, `text-wrap: balance`.
- Numbers right-aligned with `font-variant-numeric: tabular-nums`.
- Below about 36rem of container width (`@container (width < 36rem)`), rows become stacked
  cards with each cell's label from `data-label`; the cell text stays `--text-md`.

### Focus

```css
:where(a, button, input, select, textarea, summary, [tabindex]):focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 3px;
}
```

On every site, on every focusable element, with nothing removing it. `--accent` is at
least 10:1 against every surface. Outlines survive Windows High Contrast; anything shown
only by a fill or `box-shadow` (the current nav item, a pressed toggle) also needs an
outline or border inside `@media (forced-colors: active)`.

### Footer

`--muted` at `--text-md` (credits and legal lines may be `--text-sm`), links as above,
a `--line` rule above it.

### Also on every site

- `<meta name="color-scheme" content="dark">` in the HTML head.
- `::selection` in `--accent-soft` with `--ink` text.
- Motion only under `@media (prefers-reduced-motion: no-preference)`.

## Checking a site against this spec

- Every visible text node is at least 14px at default zoom, and body text 16px.
- axe finds nothing; text 4.5:1, controls and focus 3:1.
- Tab through the page: every stop shows the lime ring.
- At 390px wide there's no sideways scroll; at 200% zoom nothing is cut off.
- The masthead's mark goes to durishlabs.com.
