# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Author

Developed by **Pablo Moratinos**, Lead of the Data & Experimentation team at **Product Hackers**.

## Project Overview

CSV Viewer is a **single-file web application** (`index.html`) that provides a full dashboard for exploring CSV data. No build tools, no server, no dependencies to install — just open `index.html` in a browser.

## Development

To preview changes, open the file directly in a browser:
```
open index.html
```

There is no build step, linter, or test suite. All HTML, CSS, and JS live in one file.

## Architecture

Everything is in `index.html`, structured in this order:

1. **`<style>`** — All CSS (variables in `:root`, component styles)
2. **`<body>`** — HTML structure: header, drop zone, dashboard (summary, table, chart sections)
3. **`<script>`** — All application logic in vanilla JS

### External Dependencies (CDN)
- **PapaParse 5.4.1** — CSV parsing with auto-delimiter detection
- **Chart.js 4.4.7** — Chart rendering (bar, line, pie, scatter)

### State Management
A single global `state` object holds all application state:
- `headers` / `rows` — raw parsed data
- `filtered` — rows after applying search + column filters
- `sortCol` / `sortAsc` — current sort state
- `page` — current pagination page
- `colFilters` — per-column filter strings
- `chart` — current Chart.js instance

### Key Functions
- `loadFile(file)` — parses CSV via PapaParse, populates state, triggers initial render
- `buildSummary()` — computes per-column stats (type detection, min/max/mean/median, unique/null counts)
- `applyFilters()` → `sortData()` → `renderTable()` — the filtering/rendering pipeline; any filter change triggers this chain
- `buildChartControls()` / `renderChart()` — populates axis dropdowns and draws charts; bar/line support up to 3 Y-axis variables
- `exportCSV()` / `exportChart()` — export filtered data as CSV or chart as PNG

### Column filters
Per-column filters require explicit confirmation (click ✓ button or press Enter). Global search filters on input. Both use case-insensitive substring matching.
