# TSC Room Tracker — JSU

Real-time classroom availability tracker for Jacksonville State University. Single-file HTML app, no build step, no server — just open it or host it on GitHub Pages.

## What it does

Shows which campus rooms are free right now, what's coming up, and when the next open window is. Pulls from 25Live schedule data baked directly into the file.

## Current data

- **Terms:** Spring 2026 (Jan 7 – Apr 28) and Summer 2026 (May 7 – Jul 29)
- **Coverage:** 20 buildings, 198 rooms, 1,085 schedule entries, 357 instructors
- **Auto-switching:** The app detects today's date and shows the correct term automatically. No manual toggle needed.

### Buildings

| Code | Name | Code | Name |
|------|------|------|------|
| AH | Ayers Hall | MB | Merrill Building |
| BH | Brewer Hall | MCC | McClellan |
| CEPS | CEPS | MCG | McGee Science Center |
| CFAF | Fine Arts | MH | Mason Hall |
| HH | Hammond Hall | RDH | Round House |
| KEN | Kennamer Hall | RH | Rowe Hall |
| LIB | Houston Cole Library | RWB | Ramona Wood Building |
| LS | Lurleen B. Wallace Hall | SC | Stone Center |
| MAH | Martin Hall | SCX | South Complex |
| SEL | Self Hall | STE | Stephenson Hall |

## Features

**Core**
- Real-time clock with term indicator
- Day selector (Mon–Fri, Saturday available in settings)
- Building filter with room counts, drag-to-reorder in settings
- Availability filters: All, Available Now, Free 30+, Free 60+
- Equipment filter pills: Mic, Camera, HDMI, Whiteboard, Active Learning
- Room search by name
- Instructor search — type a professor's name to see all their classes and rooms
- Pin system with localStorage persistence

**Room cards**
- Live status: free, occupied with countdown ("Free in 1 hr 23 min"), or free with time until next class
- Timeline bar showing the day's busy/free pattern at a glance
- Equipment badges
- Class count for the selected day

**Detail view**
- Full day schedule sorted chronologically with NOW badge on active class
- Instructor names resolved from 25Live data
- Room technology info (projector, TV, AV system, features)
- ICS export — pick free windows and download a .ics calendar file
- Share link — copies a deep URL like `#AH/359/Monday` to clipboard

**Building stats**
- Banner showing "X of Y rooms free now" for the selected building

**Settings**
- 12 themes: Stay Cocky (JSU red), Jobs (iOS), Woz (classic Mac), Terminal, OLED, Tesla, Gotham, Obsidian, Game Boy, Synthwave, Phosphor, Night Game
- Dark/light auto-toggle follows OS `prefers-color-scheme` until a theme is manually selected
- Saturday toggle
- Building order drag-reorder
- Pinned rooms management

**Deep linking**
- Hash routes: `index.html#AH/359/Monday` opens directly to a room's detail view
- Shareable via the 🔗 button in the detail header

## Updating schedule data

The schedule is embedded as a JS object inside the HTML file. To update it:

1. **Export from 25Live:** Run an Event Listing report filtered to the target term(s). Export as CSV. Required columns: `EVENT_TITLE`, `LOCATIONS`, `START_TIME`, `END_TIME`, `REPEATING_RULE`, `DESCRIPTION`, `INSTRUCTOR`, `CUSTOM_ATTRIBUTES`.

2. **Parse the CSV:** The CSV has one row per section. Key mappings:
   - `LOCATIONS` → building code + room (e.g., `AH 200D` → `bldg: "AH"`, `room: "200D"`)
   - `REPEATING_RULE` → day code (`W1 TU 2025-12-09T...` → `"Tuesday"`)
   - `START_TIME` / `END_TIME` → `s` / `e` in `HH:MM` format
   - `EVENT_TITLE` → `course`
   - `INSTRUCTOR` email → `instr` (username before @)
   - `CUSTOM_ATTRIBUTES` → filter by `SIS Term Code`

3. **Replace the data:** Swap out the `const SCHEDULE = {...};` and `const INSTRUCTORS = {...};` blocks. Update the `TERMS` array with new start/end dates.

4. **Update BLDG_NAMES** if new building codes appear in the export.

### Data notes

- Rows without a `LOCATIONS` value are online/TBA sections and are skipped (~16% of CSV rows)
- Pipe-separated room keys from older exports (e.g., `357|AH 363`) are handled as separate entries in the new parser
- Near-duplicate course names (`& ` vs ` and `) are normalized during parsing
- Each entry carries a `term` field (`sp26` or `su26`) so the app can filter at runtime

## Equipment data

Room technology info (`const EQUIPMENT`) is maintained manually and is separate from the 25Live export. It covers AH, BH, CEPS, LIB, MAH, MB, RWB, RDH, SCX, and SC. To update, edit the `EQUIPMENT` object directly.

## Hosting

Drop `index.html` anywhere that serves static files. GitHub Pages, a local file:// URL, or any web server. No dependencies, no API calls, no CORS concerns. Works offline once loaded.

## Tech

- Single-file HTML (~217 KB)
- Vanilla JS, no frameworks
- CSS custom properties for theming
- Google Fonts: Rajdhani, DM Sans, Courier Prime
- localStorage for pins, theme, building order, Saturday toggle
