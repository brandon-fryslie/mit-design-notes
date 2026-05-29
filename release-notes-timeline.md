# Release Notes — Aggregated Timeline (compact, dark/light)

A dense-but-readable timeline that aggregates release notes across **packages →
modules → individual changes** into one chronological feed. Dark theme is the
default; a light theme is one attribute away; a sun/moon icon toggles them.

Public domain. Copy freely.

---

## Design intent

- **Hierarchy = domain model.** `Day → Package → Module → Change`. The structure
  is always visible; density comes from thin chrome, not from hiding levels.
- **One discriminator drives all change-type color.** Every change element carries
  `data-type="…"`. That maps to two custom properties — `--type` (solid) and
  `--type-soft` (tint). The dot, the badge, and the row's left accent all read
  those two vars. Add a new type later = add one token line per theme. Nothing
  else changes.
- **Theme is data.** `data-theme="light"` on `<html>` swaps one token set. No
  component branches on theme; both renders run identical CSS. Dark is `:root`.
- **Compact, scannable.** 13px base, tight rows, monospace version chips, sticky
  day + package headers so context never scrolls away.

---

## The stylesheet

Drop this in a `<style>` tag (or `.css` file). Do not edit token *values* when
building markup — read them.

```css
/* ============================================================
   TOKENS — DARK (default)
   ============================================================ */
:root {
  color-scheme: dark;

  /* surfaces */
  --bg:              #0b0d12;
  --surface:         #121620;
  --surface-raised:  #181d29;
  --surface-hover:   #1c2230;
  --border:          #242b39;
  --border-strong:   #313a4d;

  /* text */
  --text:            #e7eaf0;
  --text-secondary:  #9da7b8;
  --text-muted:      #687085;

  /* accent (interactive / brand) */
  --accent:          #7c8cff;
  --accent-hover:    #98a4ff;
  --accent-soft:     rgba(124, 140, 255, 0.12);

  /* change-type palette: solid + soft tint (12% alpha) */
  --c-feature:       #45c08a;  --c-feature-soft:     rgba(69, 192, 138, 0.13);
  --c-fix:           #5aa9f5;  --c-fix-soft:         rgba(90, 169, 245, 0.13);
  --c-improvement:   #4fd0d6;  --c-improvement-soft: rgba(79, 208, 214, 0.13);
  --c-breaking:      #f2647a;  --c-breaking-soft:    rgba(242, 100, 122, 0.14);
  --c-deprecated:    #e0a94a;  --c-deprecated-soft:  rgba(224, 169, 74, 0.13);
  --c-security:      #b483f0;  --c-security-soft:    rgba(180, 131, 240, 0.14);
  --c-perf:          #f0954a;  --c-perf-soft:        rgba(240, 149, 74, 0.13);
  --c-docs:          #8b93a8;  --c-docs-soft:        rgba(139, 147, 168, 0.13);

  /* elevation */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.35);
  --shadow-md: 0 4px 16px rgba(0,0,0,0.40);

  /* typography */
  --font-sans: 'Inter', ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --font-mono: ui-monospace, "SF Mono", "JetBrains Mono", "Cascadia Code", Menlo, monospace;
  --fs-xs: 11px; --fs-sm: 12px; --fs-base: 13px; --fs-md: 14px;
  --fs-lg: 16px; --fs-xl: 20px; --fs-2xl: 26px;
  --lh-tight: 1.25; --lh-normal: 1.5;
  --fw-regular: 400; --fw-medium: 500; --fw-semibold: 600; --fw-bold: 700;

  /* spacing */
  --sp-05: 2px; --sp-1: 4px; --sp-15: 6px; --sp-2: 8px; --sp-3: 12px;
  --sp-4: 16px; --sp-5: 20px; --sp-6: 24px; --sp-7: 32px;

  /* radius */
  --r-xs: 4px; --r-sm: 6px; --r-md: 8px; --r-lg: 12px; --r-full: 999px;

  /* layout */
  --content-max: 1080px;
  --spine: 1px;            /* timeline rail width */
  --spine-gap: 18px;       /* indent from rail to content */
}

/* ============================================================
   TOKENS — LIGHT (override only; same component CSS)
   ============================================================ */
:root[data-theme="light"] {
  color-scheme: light;

  --bg:              #f6f7f9;
  --surface:         #ffffff;
  --surface-raised:  #ffffff;
  --surface-hover:   #f1f3f7;
  --border:          #e4e8ee;
  --border-strong:   #d2d8e2;

  --text:            #1b2027;
  --text-secondary:  #59616f;
  --text-muted:      #878f9d;

  --accent:          #5663e6;
  --accent-hover:    #4250d4;
  --accent-soft:     rgba(86, 99, 230, 0.10);

  --c-feature:       #1f9d6b;  --c-feature-soft:     rgba(31, 157, 107, 0.11);
  --c-fix:           #2f7fd4;  --c-fix-soft:         rgba(47, 127, 212, 0.10);
  --c-improvement:   #1496a0;  --c-improvement-soft: rgba(20, 150, 160, 0.10);
  --c-breaking:      #d23b56;  --c-breaking-soft:    rgba(210, 59, 86, 0.10);
  --c-deprecated:    #b07d1f;  --c-deprecated-soft:  rgba(176, 125, 31, 0.11);
  --c-security:      #8a4fd0;  --c-security-soft:    rgba(138, 79, 208, 0.10);
  --c-perf:          #cf6a1f;  --c-perf-soft:        rgba(207, 106, 31, 0.10);
  --c-docs:          #5b6678;  --c-docs-soft:        rgba(91, 102, 120, 0.10);

  --shadow-sm: 0 1px 2px rgba(20,30,55,0.06);
  --shadow-md: 0 6px 20px rgba(20,30,55,0.10);
}

/* ============================================================
   CHANGE-TYPE MAP — the single discriminator.
   Each data-type sets two vars; all visuals read them.
   ============================================================ */
[data-type="feature"]     { --type: var(--c-feature);     --type-soft: var(--c-feature-soft); }
[data-type="fix"]         { --type: var(--c-fix);         --type-soft: var(--c-fix-soft); }
[data-type="improvement"] { --type: var(--c-improvement); --type-soft: var(--c-improvement-soft); }
[data-type="breaking"]    { --type: var(--c-breaking);    --type-soft: var(--c-breaking-soft); }
[data-type="deprecated"]  { --type: var(--c-deprecated);  --type-soft: var(--c-deprecated-soft); }
[data-type="security"]    { --type: var(--c-security);    --type-soft: var(--c-security-soft); }
[data-type="perf"]        { --type: var(--c-perf);        --type-soft: var(--c-perf-soft); }
[data-type="docs"]        { --type: var(--c-docs);        --type-soft: var(--c-docs-soft); }

/* ============================================================
   BASE
   ============================================================ */
* { box-sizing: border-box; }
html { -webkit-text-size-adjust: 100%; }
body {
  margin: 0;
  background: var(--bg);
  color: var(--text);
  font-family: var(--font-sans);
  font-size: var(--fs-base);
  line-height: var(--lh-normal);
  -webkit-font-smoothing: antialiased;
  text-rendering: optimizeLegibility;
}
a { color: var(--accent); text-decoration: none; }
a:hover { color: var(--accent-hover); text-decoration: underline; }
:focus-visible { outline: 2px solid var(--accent); outline-offset: 2px; border-radius: var(--r-xs); }

::-webkit-scrollbar { width: 10px; height: 10px; }
::-webkit-scrollbar-thumb { background: var(--border-strong); border-radius: var(--r-full); border: 2px solid var(--bg); }
::-webkit-scrollbar-thumb:hover { background: var(--text-muted); }

/* ============================================================
   PAGE + TOPBAR
   ============================================================ */
.page { max-width: var(--content-max); margin: 0 auto; padding: 0 var(--sp-5) var(--sp-7); }

.topbar {
  position: sticky; top: 0; z-index: 30;
  display: flex; align-items: center; gap: var(--sp-4);
  padding: var(--sp-3) var(--sp-5);
  margin: 0 calc(-1 * var(--sp-5)) var(--sp-4);
  background: color-mix(in srgb, var(--bg) 86%, transparent);
  backdrop-filter: saturate(140%) blur(8px);
  border-bottom: 1px solid var(--border);
}
.topbar__title { font-size: var(--fs-lg); font-weight: var(--fw-semibold); letter-spacing: -0.01em; margin: 0; }
.topbar__spacer { flex: 1; }

.search {
  display: flex; align-items: center; gap: var(--sp-2);
  height: 30px; padding: 0 var(--sp-3);
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--r-sm); color: var(--text-secondary);
  min-width: 220px;
}
.search input {
  border: 0; background: transparent; outline: 0; color: var(--text);
  font: inherit; font-size: var(--fs-sm); width: 100%;
}
.search input::placeholder { color: var(--text-muted); }

/* icon button (theme toggle lives here) */
.icon-btn {
  display: inline-flex; align-items: center; justify-content: center;
  width: 30px; height: 30px; padding: 0;
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--r-sm); color: var(--text-secondary); cursor: pointer;
  transition: color .12s, border-color .12s, background .12s;
}
.icon-btn:hover { color: var(--text); border-color: var(--border-strong); background: var(--surface-hover); }
.icon-btn svg { width: 16px; height: 16px; }
/* show the right glyph per theme: sun in dark, moon in light */
.icon-btn .i-sun  { display: block; }
.icon-btn .i-moon { display: none; }
:root[data-theme="light"] .icon-btn .i-sun  { display: none; }
:root[data-theme="light"] .icon-btn .i-moon { display: block; }

/* ============================================================
   FILTER CHIPS (filter by change type)
   ============================================================ */
.filters { display: flex; flex-wrap: wrap; gap: var(--sp-15); margin-bottom: var(--sp-4); }
.chip {
  display: inline-flex; align-items: center; gap: var(--sp-15);
  height: 26px; padding: 0 10px;
  font-size: var(--fs-xs); font-weight: var(--fw-medium); letter-spacing: .02em;
  color: var(--text-secondary); background: var(--surface);
  border: 1px solid var(--border); border-radius: var(--r-full);
  cursor: pointer; user-select: none; transition: all .12s;
}
.chip:hover { border-color: var(--border-strong); color: var(--text); }
.chip__dot { width: 7px; height: 7px; border-radius: var(--r-full); background: var(--type, var(--text-muted)); }
.chip[aria-pressed="true"] { color: var(--type, var(--accent)); background: var(--type-soft, var(--accent-soft)); border-color: transparent; }

/* ============================================================
   TIMELINE — Day > Package > Module > Change
   ============================================================ */
.timeline { position: relative; }

/* DAY group */
.day { position: relative; padding-left: calc(var(--spine-gap) + var(--sp-2)); }
.day::before {                           /* the spine */
  content: ""; position: absolute; left: 4px; top: 26px; bottom: 0;
  width: var(--spine); background: var(--border);
}
.day__header {
  position: sticky; top: 52px; z-index: 20;
  display: flex; align-items: baseline; gap: var(--sp-2);
  margin: var(--sp-4) 0 var(--sp-2) calc(-1 * (var(--spine-gap) + var(--sp-2)));
  padding: var(--sp-1) 0;
  background: var(--bg);
}
.day__date { font-size: var(--fs-sm); font-weight: var(--fw-semibold); color: var(--text); letter-spacing: .01em; }
.day__rel  { font-size: var(--fs-xs); color: var(--text-muted); }
.day__count { margin-left: auto; font-size: var(--fs-xs); color: var(--text-muted); }
.day__node {                              /* dot on the spine at the day marker */
  position: absolute; left: 0; top: 6px; width: 9px; height: 9px;
  border-radius: var(--r-full); background: var(--accent);
  box-shadow: 0 0 0 3px var(--bg);
}

/* PACKAGE group — collapsible */
.pkg { margin-bottom: var(--sp-1); }
.pkg__header {
  display: flex; align-items: center; gap: var(--sp-2);
  width: 100%; text-align: left; cursor: pointer;
  padding: var(--sp-15) var(--sp-2);
  background: transparent; border: 0; border-radius: var(--r-sm);
  color: var(--text); font: inherit;
  transition: background .12s;
}
.pkg__header:hover { background: var(--surface-hover); }
.pkg__caret { color: var(--text-muted); transition: transform .15s; flex: none; }
.pkg[data-open="false"] .pkg__caret { transform: rotate(-90deg); }
.pkg__name { font-size: var(--fs-md); font-weight: var(--fw-semibold); letter-spacing: -0.005em; }
.pkg__meta { margin-left: auto; display: flex; gap: var(--sp-2); font-size: var(--fs-xs); color: var(--text-muted); }
.pkg__meta b { color: var(--text-secondary); font-weight: var(--fw-semibold); }
.pkg__body { padding-left: var(--sp-3); }
.pkg[data-open="false"] .pkg__body { display: none; }

/* MODULE sub-group — thin label, then its changes */
.module { padding: var(--sp-1) 0 var(--sp-2); }
.module__label {
  display: inline-flex; align-items: center; gap: var(--sp-15);
  font-family: var(--font-mono); font-size: var(--fs-xs);
  color: var(--text-muted); letter-spacing: .01em;
  padding: var(--sp-05) 0; margin-bottom: var(--sp-05);
}
.module__label::before { content: "›"; color: var(--border-strong); }

/* CHANGE row — the leaf. Grid keeps columns aligned and tight. */
.change {
  display: grid;
  grid-template-columns: 16px minmax(0, 1fr) auto;
  align-items: start; column-gap: var(--sp-2);
  padding: var(--sp-15) var(--sp-2);
  border-left: 2px solid transparent;
  border-radius: 0 var(--r-sm) var(--r-sm) 0;
  transition: background .1s;
}
.change:hover { background: var(--surface-hover); border-left-color: var(--type); }

.change__dot {
  width: 8px; height: 8px; margin-top: 5px; justify-self: center;
  border-radius: var(--r-full); background: var(--type);
}
.change__main { min-width: 0; }
.change__title {
  font-size: var(--fs-base); color: var(--text); line-height: var(--lh-tight);
  display: flex; align-items: center; gap: var(--sp-2); flex-wrap: wrap;
}
.change__desc {
  font-size: var(--fs-sm); color: var(--text-secondary); line-height: var(--lh-normal);
  margin-top: var(--sp-05);
}

/* type badge — reads the same two vars as the dot */
.badge {
  display: inline-flex; align-items: center; height: 17px; padding: 0 var(--sp-15);
  font-size: 10px; font-weight: var(--fw-semibold); letter-spacing: .04em; text-transform: uppercase;
  color: var(--type); background: var(--type-soft); border-radius: var(--r-xs);
  white-space: nowrap; flex: none;
}

/* right column: version chip + time */
.change__meta { display: flex; align-items: center; gap: var(--sp-2); white-space: nowrap; padding-top: 1px; }
.version {
  font-family: var(--font-mono); font-size: var(--fs-xs); color: var(--text-secondary);
  background: var(--surface); border: 1px solid var(--border);
  padding: 1px var(--sp-15); border-radius: var(--r-xs);
}
.time { font-size: var(--fs-xs); color: var(--text-muted); }

/* breadcrumb (only when changes are shown flat, outside a module group) */
.change__path { font-family: var(--font-mono); font-size: var(--fs-xs); color: var(--text-muted); }
.change__path b { color: var(--text-secondary); font-weight: var(--fw-medium); }

/* empty / filtered-out state */
.empty { text-align: center; color: var(--text-muted); font-size: var(--fs-sm); padding: var(--sp-7) 0; }

@media (prefers-reduced-motion: reduce) { * { transition: none !important; } }

@media (max-width: 640px) {
  .change { grid-template-columns: 16px minmax(0, 1fr); }
  .change__meta { grid-column: 2; padding-top: var(--sp-05); }
  .search { min-width: 0; flex: 1; }
}
```

---

## Build prompt

> Paste this prompt plus the stylesheet above into your LLM. Replace the sample
> data note with your real release-note data if you have it.

```
Build a single self-contained `index.html` for an aggregated release-notes
timeline. Use the CSS I provide VERBATIM in a <style> block — do not change any
token values, class names, or selectors. Your job is the HTML markup, sample
data, and the minimal JS for the three interactions below.

DOMAIN
- The page aggregates release notes across many PACKAGES. Each package has
  multiple MODULES. Each module emits individual CHANGES (release-note entries).
- A change has: type (one of: feature, fix, improvement, breaking, deprecated,
  security, perf, docs), a short title, an optional one-line description, a
  semver version string, and a timestamp.

STRUCTURE (match the CSS classes exactly)
- .page > .topbar (title "Release Notes" + .search + theme .icon-btn) > .filters
  (one .chip per change type) > .timeline.
- Inside .timeline, group by DAY (newest first): .day with .day__node, sticky
  .day__header (.day__date, .day__rel e.g. "3 days ago", .day__count e.g.
  "12 changes").
- Within each day, group by PACKAGE: .pkg (with data-open="true"/"false") whose
  .pkg__header has a caret (.pkg__caret), .pkg__name, and .pkg__meta showing
  "<b>N</b> modules" and "<b>M</b> changes".
- Within each package, group by MODULE: .module with a .module__label (module
  name, monospace).
- Each change is a .change element carrying data-type="<type>", containing
  .change__dot, .change__main (.change__title with a <span class="badge"> for the
  type label + the title text; .change__desc for the description), and
  .change__meta (.version chip + .time).
- The badge, dot, and hover accent all inherit color from data-type — never set
  per-change colors inline.

THEME
- Default theme is dark: put nothing or data-theme on <html> for dark.
- Light theme = <html data-theme="light">.
- The .icon-btn contains TWO inline SVGs: <svg class="i-sun">…</svg> and
  <svg class="i-moon">…</svg> (the CSS shows the correct one per theme; you do
  not toggle their visibility in JS).
- JS toggle: on click, flip data-theme between absent (dark) and "light", persist
  to localStorage under key "rn-theme". On load, read localStorage; if unset,
  honor prefers-color-scheme. Apply before first paint to avoid a flash.

INTERACTIONS (minimal vanilla JS, no libraries)
1. Theme toggle (above).
2. Package collapse: clicking .pkg__header flips its data-open attribute.
3. Filter + search: .chip elements (each carrying data-type="<type>" so its
   .chip__dot and pressed-state color resolve, plus aria-pressed) filter visible
   changes by type; multiple
   chips OR together; none pressed = show all. The .search input filters changes
   whose title/description/package/module text matches. Hide .change elements
   that don't match; hide any .module / .pkg / .day that ends up empty; show the
   .empty state if nothing matches.

DATA
- Include ~3 packages, 2-3 modules each, spread across 3-4 days, with a realistic
  mix of all change types so every color is visible. Keep titles terse and
  technical.

QUALITY BAR
- Compact and scannable; semantic HTML; buttons are real <button>s; the search
  input has a label; keyboard-focusable; respects prefers-reduced-motion (already
  in CSS). No console errors. No external requests or fonts beyond the system
  stack already in the tokens.
```
