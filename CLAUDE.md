# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Daily Health Tracker — a single-file, client-side web application for tracking supplements, weight/body fat, and workouts. No build tools, no dependencies, no backend.

## Running

Open `supplements.html` directly in a browser. No server, build step, or install required.

## Architecture

The entire application lives in **one file** (`supplements.html`) with embedded CSS and JavaScript:

- **Lines 7–820**: CSS with custom properties for dark theme (`:root` variables)
- **Lines 824–1178**: HTML markup with three tab panels
- **Lines 1180–2213**: JavaScript — all application logic

### Data Layer

All state is stored in `localStorage` under the key `healthTracker`. The `getData()` / `saveData()` functions are the single read/write interface to storage. The data schema:

```
{
  supplements: { date, checked: {}, streak, lastCompleted },
  cycles: { boron: { startDate }, ashwa: { startDate } },
  weight: { [dateStr]: { weight, bf } },
  workouts: { [dateStr]: [types] },
  steps: { [dateStr]: stepCount }
}
```

### Three Modules

1. **Supplements** — 13+ items across Morning/Afternoon/Evening time slots. Tracks daily completion streak. Manages on/off cycling for Boron (14 on / 7 off) and Ashwagandha (30 on / 7 off).
2. **Weight** — Logs weight (kg) and body fat (%). Custom Canvas-based line charts with 7D/14D/30D range selector. Validation: weight 20–300 kg, body fat 1–60%.
3. **Workouts** — Push/Pull/Legs strength tracking, cardio (Zone 2, HIIT), daily steps. Rest mode is exclusive (clears other selections).

### Daily Quote Banner

A rotating motivational quote displayed between the header and tab bar. 49 quotes from Greg Plitt, Eric Thomas, Arnold Schwarzenegger, Muhammad Ali, and others. The quote is selected deterministically using day-of-year modulo the array length (`QUOTES` array, `getDailyQuote()` function) — no stored state.

### Key Patterns

- Tab switching is DOM class toggling (`active` class on `.tab` and `.tab-content`)
- Charts are drawn directly on `<canvas>` elements — no charting library
- Cycle status is calculated from `startDate` + elapsed days, not stored state
- Import/export uses JSON file download/upload via FileReader API
- Historical entries are editable via click-to-reveal edit panels
