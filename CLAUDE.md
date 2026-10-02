# Project: time-and-date

## Overview
Single-page web app that shows the current time in cities around the world. No build tools, no dependencies — pure HTML/CSS/JS in a single file.

## Files
- `index.html` — the entire application (HTML + CSS + JS, self-contained)

## Architecture
- **Data**: 200+ cities hardcoded in a `CITIES` array. Each entry has `name`, `country`, `tz` (IANA timezone), and `lat` (latitude for sort order). Americas-heavy coverage with secondary cities (e.g. Chihuahua, Punta Arenas, Isla de Pascua). A subset of entries are **timezone zone entries** (not cities) — they have a `codes` array field (e.g. `codes: ["EST","EDT","ET"]`) and use the timezone code as their `name`. These 12 entries represent the US timezone codes from Wikipedia (SST, HST, HST/HDT, AKST/AKDT, PST/PDT, MST fixed, MST/MDT, CST/CDT, EST/EDT, AST, ChST) plus UTC/GMT. **Never add `codes` to a real city** — `sortByTimezone()` treats any entry with `codes` as a zone entry, not a city.
- **US states**: every entry with `country:"Estados Unidos"` (84 entries: all 50 state capitals + Washington D.C. + Puerto Rico, Guam, U.S. Virgin Islands, American Samoa, Northern Mariana Islands) carries `state` (Spanish name), `stateCode` (2-letter USPS-style code, used for exact-match search), and `stateEn` only when the English name differs from the Spanish one (e.g. `state:"Nuevo México", stateEn:"New Mexico"`). Washington D.C.'s entry also sets `hideState:true` since the state name would be redundant with the city name. These fields are additive — `name` and `tz` (which form `cityKey()`) are never changed on existing entries, so renaming/relabeling never invalidates a user's `localStorage`.
- **Persistence**: Selected cities in `localStorage` key `worldtime_v1`; active theme in `worldtime_theme`; active language in `worldtime_lang`; home city key in `worldtime_home`.
- **Time display**: Uses `Intl.DateTimeFormat` with each city's IANA timezone. Clocks tick every second via `setInterval`.
- **Sort order**: Manual order stored in `selectedCities` array (insertion order by default). ORD button sorts ascending by local time (seconds-of-day via `getSecondOfDay()`); on ties, timezone zone entries (those with `codes`) come before real cities (sorted alphabetically by name), then real cities sort north-to-south by latitude. Drag & drop reorders the array directly. Order is persisted to `localStorage` on every change.
- **Re-render strategy**: Every second only clock/date/offset text nodes are updated in-place via `tickUpdate()` to avoid layout thrash. `renderFull()` is called only on structural changes (add, remove, reorder, home toggle, language change).
- **Theming**: CSS custom properties on `[data-theme]` attribute of `<html>`. Two themes: `dark` (#075056 , cards #054044, home card #0a6870, accent #7dd4d8, text #EEF7FF) and `light` (gradient #9ec8e0→#cce6f2→#eef7ff, text/accent #075056, cards white semi-transparent, home card rgba(7,80,86,0.10)). Persisted in `worldtime_theme`.
- **i18n**: `TRANSLATIONS` object with `es` and `en` keys. Active locale in `currentLang`. Auto-detected from `navigator.language` on first visit (default `en` if not Spanish); persisted in `worldtime_lang`. City entries carry `country`/`countryEn` and optionally `nameEn` for cities with different English names. `lookupCity()` resolves stored objects against the full CITIES array. Search matches against `name`, `nameEn`, `country`, and `countryEn`.
- **DST detection**: `isInDST(tz)` compares UTC offsets in January vs June via `Intl.DateTimeFormat` to determine the DST offset (`Math.max(jan, jun)`), then checks if the current offset matches it. Result cached per-minute in `_dstCache`. Works for both hemispheres with no hardcoded dates. The DST badge `<em class="dst-badge">` is rendered inline inside `.city-offset`, which uses `display: flex; align-items: center` to keep the badge vertically aligned with the offset text.
- **Home city**: Stored as a `cityKey` string in `worldtime_home`. Home card gets `.card.home` CSS class (lighter background). Home city cannot be removed — the ✕ button is hidden while it is set as home. Cleared automatically if the city is removed.
- **Search**: The autocomplete filter uses `matchScore(c)` to prioritize results: score 0 = match via `c.codes`, score 1 = match via `name`/`nameEn`, score 2 = match via `state`/`stateEn` (substring) or `stateCode` (exact match only, to avoid 2-letter codes shadowing common-word city names), score 3 = match via `country`/`countryEn`. Scores are precomputed once per keystroke (not inside the sort comparator) for performance. Results are sorted by score before slicing to 12, so timezone code entries always appear before state-matched cities, which appear before country-only matches. Pressing **Enter** in the search field adds the first visible dropdown result immediately.
- **Desktop detection**: `isDesktop = matchMedia('(hover: hover) and (pointer: fine)')`. Drag & drop and ORD button are only enabled on desktop.
- **Drag & drop**: HTML5 drag API on `.card[draggable]` elements. `dragSrcKey` tracks the dragged card's key. On drop, splices `selectedCities` and saves. `initDrag()` is called after every `renderFull()`.

## Features
- Autocomplete search field (filters by city name, US state — full name in ES/EN or 2-letter code —, timezone code, or country; accent-insensitive; matches ES/EN names and `codes` field). Timezone code entries (e.g. "EST", "UTC") are prioritized over state- and country-name matches.
- US city cards show the state in the title (e.g. "Austin, Texas"); the second line stays as the country ("Estados Unidos"). Washington D.C. omits the state (`hideState`) since it would duplicate the city name.
- Dropdown shows up to 12 matches with city/zone name, country/zone description, and live UTC offset; click or press **Enter** to add the first result
- Each city card shows: local time (HH:MM:SS), date, country, UTC offset
- ☀ DST badge next to offset when the city is currently observing Daylight Saving Time
- ⌂ button on each card to mark/unmark as home city (highlighted background, cannot be removed while set)
- ✕ button on each card to remove it (hidden for the home city)
- ORD button (desktop only): sorts all cards by local time ascending; on ties, timezone zone entries come first (alphabetically), then real cities (north-to-south)
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
