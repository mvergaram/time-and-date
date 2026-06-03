# Project: time-and-date

## Overview
Single-page web app that shows the current time in cities around the world. No build tools, no dependencies — pure HTML/CSS/JS in a single file.

## Files
- `index.html` — the entire application (HTML + CSS + JS, self-contained)

## Architecture
- **Data**: 200+ cities hardcoded in a `CITIES` array. Each entry has `name`, `country`, `tz` (IANA timezone), and `lat` (latitude for sort order). Americas-heavy coverage with secondary cities (e.g. Chihuahua, Punta Arenas, Isla de Pascua).
- **Persistence**: Selected cities in `localStorage` key `worldtime_v1`; active theme in `worldtime_theme`; active language in `worldtime_lang`; home city key in `worldtime_home`.
- **Time display**: Uses `Intl.DateTimeFormat` with each city's IANA timezone. Clocks tick every second via `setInterval`.
- **Sort order**: Manual order stored in `selectedCities` array (insertion order by default). ORD button sorts ascending by local time (seconds-of-day via `getSecondOfDay()`), then north-to-south by latitude on ties. Drag & drop reorders the array directly. Order is persisted to `localStorage` on every change.
- **Re-render strategy**: Every second only clock/date/offset text nodes are updated in-place via `tickUpdate()` to avoid layout thrash. `renderFull()` is called only on structural changes (add, remove, reorder, home toggle, language change).
- **Theming**: CSS custom properties on `[data-theme]` attribute of `<html>`. Two themes: `dark` (#075056 bg teal oscuro, cards #054044, home card #0a6870, acento #7dd4d8, texto #EEF7FF) and `light` (gradient #9ec8e0→#cce6f2→#eef7ff bg azul cielo pálido, texto/acento #075056, cards blanco semi-transparente, home card rgba(7,80,86,0.10)). Persisted in `worldtime_theme`. Colors inspired by Perficient brand palette.
- **i18n**: `TRANSLATIONS` object with `es` and `en` keys. Active locale in `currentLang`. Auto-detected from `navigator.language` on first visit (default `en` if not Spanish); persisted in `worldtime_lang`. City entries carry `country`/`countryEn` and optionally `nameEn` for cities with different English names. `lookupCity()` resolves stored objects against the full CITIES array. Search matches against `name`, `nameEn`, `country`, and `countryEn`.
- **DST detection**: `isInDST(tz)` compares UTC offsets in January vs June via `Intl.DateTimeFormat` to determine the DST offset (`Math.max(jan, jun)`), then checks if the current offset matches it. Result cached per-minute in `_dstCache`. Works for both hemispheres with no hardcoded dates.
- **Home city**: Stored as a `cityKey` string in `worldtime_home`. Home card gets `.card.home` CSS class (lighter background). Home city cannot be removed — the ✕ button is hidden while it is set as home. Cleared automatically if the city is removed.
- **Desktop detection**: `isDesktop = matchMedia('(hover: hover) and (pointer: fine)')`. Drag & drop and ORD button are only enabled on desktop.
- **Drag & drop**: HTML5 drag API on `.card[draggable]` elements. `dragSrcKey` tracks the dragged card's key. On drop, splices `selectedCities` and saves. `initDrag()` is called after every `renderFull()`.

## Features
- Autocomplete search field (filters by city name or country, accent-insensitive, matches both ES/EN names)
- Dropdown shows up to 12 matches with city name, country, and live UTC offset; click to add city
- Each city card shows: local time (HH:MM:SS), date, country, UTC offset
- ☀ DST badge next to offset when the city is currently observing Daylight Saving Time
- ⌂ button on each card to mark/unmark as home city (highlighted background, cannot be removed while set)
- ✕ button on each card to remove it (hidden for the home city)
- ORD button (desktop only): sorts all cards by timezone ascending then north-to-south
- Drag & drop reordering (desktop only): drag a card onto another to swap positions
- Light/dark theme toggle (☀/☾ button), persisted in localStorage
- Language toggle (ES/EN button), auto-detected from browser, persisted in localStorage; `<title>` updates with language
- Footer: "Creado por Miguel gracias a Claude Code" / "Created by Miguel thanks to Claude Code"
- Responsive grid layout

## Conventions
- No external libraries or CDN dependencies
- All logic in a single `<script>` block at the bottom of `index.html`
- HTML output escaped via `escHtml()` / `escAttr()` helpers to prevent XSS
- UI strings accessed via `t(key)` helper against `TRANSLATIONS[currentLang]`
- Nested template literals in `renderFull()` cause syntax errors — use string concatenation for the inner expression when conditionally rendering HTML inside a template literal

# Workflow Orchestration

## Plan Mode Default
- Enter plan mode for ANY non-trivial task (3+ steps or architectural decisions)
- If something goes sideways, STOP and re-plan immediately
- Use plan mode for verification steps, not just building
- Write detailed specs upfront to reduce ambiguity

## Subagent Strategy
- Use subagents liberally to keep main context window clean
- Offload research, exploration, and parallel analysis to subagents
- For complex problems, throw more compute at it via subagents
- One task per subagent for focused execution

## Self-Improvement Loop
- After ANY correction from the user: update tasks/lessons.md with the pattern
- Write rules for yourself that prevent the same mistake
- Ruthlessly iterate on these lessons until mistake rate drops
- Review lessons at session start for relevant project

## Verification Before Done
- Never mark a task complete without proving it works
- Diff behavior between main and your changes when relevant
- Ask yourself: "Would a staff engineer approve this?"
- Run tests, check logs, demonstrate correctness

## Demand Elegance (Balanced)
- For non-trivial changes: pause and ask "is there a more elegant way?"
- If a fix feels hacky: "Knowing everything I know now, implement the elegant solution"
- Skip this for simple, obvious fixes — don't over-engineer
- Challenge your own work before presenting it

## Autonomous Bug Fixing
- When given a bug report: just fix it. Don't ask for hand-holding
- Point at logs, errors, failing tests — then resolve them
- Zero context switching required from the user
- Go fix failing CI tests without being told how

# Task Management

1. **Plan First**: Write plan to tasks/todo.md with checkable items
2. **Verify Plan**: Check in before starting implementation
3. **Track Progress**: Mark items complete as you go
4. **Explain Changes**: High-level summary at each step
5. **Document Results**: Add review section to tasks/todo.md
6. **Capture Lessons**: Update tasks/lessons.md after corrections

# Core Principles

- **Simplicity First**: Make every change as simple as possible. Impact minimal code.
- **No Laziness**: Find root causes. No temporary fixes. Senior developer standards.
- **Minimal Impact**: Changes should only touch what's necessary. Avoid introducing bugs.
