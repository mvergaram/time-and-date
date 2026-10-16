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

Fully private — this app runs entirely in your browser. No data is ever sent to any server: there's no backend, no analytics, and no tracking of any kind. All information (selected cities, theme, language, and home city) is stored only in your browser's `localStorage` (`worldtime_v1`, `worldtime_theme`, `worldtime_lang`, `worldtime_home`) and never leaves your device.

## Terms of Use

This app is provided "as is", without warranty of any kind, express or implied, including but not limited to the accuracy of displayed times, availability, or fitness for a particular purpose. You use it at your own risk and are fully responsible for your use of this app; the author assumes no liability for any damages or losses arising from it.

## License

Licensed under [GPL-3.0](LICENSE) — you're free to use, copy, modify, and distribute this code for any purpose, provided that any derivative work also remains open source under the same license.

## Created by

Miguel, with help from [Claude Code](https://claude.ai/code).
