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
- **Theming**: CSS custom properties on `[data-theme]` attribute of `<html>`. Two themes: `dark` (#075056 , cards #054044, home card #0a6870, accent #7dd4d8, text #EEF7FF) and `light` (gradient #9ec8e0→#cce6f2→#eef7ff, text/accent #075056, cards white semi-transparent, home card rgba(7,80,86,0.10)). Persisted in `worldtime_theme`.
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
