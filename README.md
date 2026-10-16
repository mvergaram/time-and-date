# World Time and Date

Single-page world clock web app. Shows the current time in cities around the world with no build tools, no dependencies — pure HTML/CSS/JS in a single file.

## Features

- Each card shows local time, date, country, DST and UTC offset
- Add cities from a **searchable autocomplete**, persisted in localStorage
- **DST badge** next to the offset when the city is currently observing Daylight Saving Time
- Mark a **home city** (highlighted card, cannot be removed while set)
- Manual **drag & drop** reordering (desktop only)
- ORD button: **Order cards** by local time ascending, then north-to-south (desktop only)
- Light/dark **theme toggle**, persisted in localStorage
- Spanish/English **language toggle**, auto-detected from browser language
- Fully responsive grid layout

## Usage

You have 2 options:
1. [Open it directly](https://mvergaram.github.io/time-and-date/)
2. Download and Open `index.html` directly in a browser — no server required.

## Tech

- Zero dependencies, zero build step
- `Intl.DateTimeFormat` for timezone handling
- `localStorage` for persistence (cities, theme, language, home city)
- DST detection via Jan/Jun UTC offset comparison — works for both hemispheres

## Privacy

Fully private. No data sent out of your browser. Locations added are hosted in your local browser storage.

## Created by

Miguel, with help from [Claude Code](https://claude.ai/code).
