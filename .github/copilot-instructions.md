<!-- .github/copilot-instructions.md: Guidance for AI coding agents working on this static portfolio -->
# Quick orientation

This repository is a small, static single-page portfolio built with plain HTML, CSS and inline JavaScript. There is no build system, package manager, or server-side code. The primary files to inspect and edit are:

- `index.html` — single-entry HTML file. Contains inline scripts for the marquee carousel, mobile gallery builder, sidenav, and basic scroll behaviour.
- `styles.css` — all styling, including responsive rules and Safari-specific workarounds. Key CSS variables: `--gap` and `--speed` in `:root`.
- `images/` — contains video and image assets referenced by `index.html` (e.g. `images/*.mp4`).

# Big-picture architecture & why

- Single-page static app: desktop uses a horizontal marquee carousel (`.marquee-track`) of videos; mobile disables the carousel and builds a vertical `.mobile-gallery` by cloning carousel items.
- Responsiveness is handled entirely with CSS media queries (`@media (max-width: 768px)`), and runtime behaviour is adjusted with an inline `isMobile` check in `index.html`.
- Several Safari/iOS workarounds exist: a head script that forces a reflow on `#marqueeTrack`, `@supports (-webkit-touch-callout: none)` CSS block that changes gap/spacing, and `-webkit-overflow-scrolling: touch` usage. Preserve these when changing layout.

# Project-specific patterns and conventions (concrete)

- Mixed naming: IDs use camelCase (e.g. `marqueeTrack`, `marqueeWrap`, `mySidenav`), classes use kebab-case (e.g. `marquee-item`, `mobile-gallery`). Follow existing naming when adding elements.
- Data-driven sizing: each carousel item uses a `data-h` attribute (e.g. `data-h="50vh"`) that the JS reads to set item heights. When adding/removing items, set `data-h` accordingly.
- No bundler — all JS is inline in `index.html`. If you extract scripts to new files, update `<script src="...">` and keep behaviour identical (events run on `load`/`resize`).
- Touch/drag handling: carousel supports mouse and touch drag. When modifying, keep the same pattern: separate drag state, `onDragStart/Move/End`, and preventDefault for horizontal touch moves only.
- Lightbox markup exists (`.lightbox` + `#lightboxImg`) but the file has no handler to populate/open it. To open, toggle `.lightbox.open` and set `document.getElementById('lightboxImg').src`.

# Integration points & external dependencies

- There are no external JS/CSS dependencies (no CDN libs or npm). The site relies on browser APIs only (requestAnimationFrame, touch events).
- Asset considerations: videos in `images/` can be large. Keep `autoplay muted loop playsinline` attributes for mobile playback. If replacing videos, test file sizes and browser memory.

# Developer workflows & commands (how to test locally)

- Preview in browser (fastest): open `index.html` directly or run a simple static server from repo root:

```bash
# macOS / zsh
cd /Users/silviadecastro/Documents/GitHub/Portfolio-2025
python3 -m http.server 8000
# then open http://localhost:8000 in your browser
```

- Use browser DevTools device toolbar to test mobile behaviour (the JS checks `window.innerWidth <= 768`). Resizing the viewport in desktop DevTools will trigger different code paths.
- When changing CSS variables (`--speed`, `--gap`) reload fully; the carousel clone/position logic reads `track.scrollWidth` on resize and on `load`.

# What to watch for when editing

- Keep Safari/iOS fixes: head reflow script, `@supports (-webkit-touch-callout: none)` block, and `-webkit-overflow-scrolling` rules. Removing them can break the marquee layout on iOS/Safari.
- If you move inline JS to external files, maintain execution timing: init routines depend on `window.load` and `resize` events. Avoid race conditions by registering handlers after DOM is ready.
- When editing carousel logic, update both desktop cloning logic and the `buildMobileGallery()` mobile code path so desktop and mobile stay consistent.
- Accessibility: page uses `lang="es"` and `role="dialog"` on lightbox. Preserve or improve ARIA attributes when changing markup.

# Examples (copyable snippets)

- Change carousel speed (CSS): update `:root { --speed: 40; }` in `styles.css`.
- Add a new item: in `index.html` add a new `.marquee-item` with `data-h` and a `<video>` or `<img>` source. Example:

```html
<div class="marquee-item" data-h="45vh">
  <video autoplay muted loop playsinline>
    <source src="images/new-file.mp4" type="video/mp4">
  </video>
  <div class="caption">Caption text</div>
</div>
```

# Editing checklist for PRs

- Confirm desktop carousel still loops smoothly and clone positioning remains correct after changes (`track.scrollWidth` logic).
- Verify mobile gallery is generated when viewport <= 768px (use DevTools device emulation), and that `.mobile-gallery` items render at 100% width.
- Test Safari (or iOS simulator) to ensure there are no visual gaps — keep the Safari-specific CSS and the head reflow script.

# If anything is unclear

Leave a short comment in the PR describing which part you changed (carousel / mobile-gallery / styles / assets). Ask reviewers to test on Desktop Chrome + Safari Mobile. If you want me to expand this file with more examples (e.g., a proposed JS refactor or moving inline scripts), tell me which area to prioritize.
