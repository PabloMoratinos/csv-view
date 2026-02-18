# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Author

Developed by **Pablo Moratinos**, Lead of the Data & Experimentation team at **Product Hackers**.

## Project Overview

CSV Viewer by Product Hackers is a **single-file web application** (`index.html`) that provides a full dashboard for exploring CSV data. No build tools, no server, no dependencies to install — just open `index.html` in a browser.

## Description

Herramienta web para explorar y analizar archivos CSV de forma visual, sin necesidad de instalación ni configuración. Solo tienes que abrir la página y cargar tu archivo. Incluye un dataset de ejemplo de ecommerce para probar la herramienta al instante.

## Features

- **Carga de datos** — Arrastra y suelta un CSV o selecciónalo desde tu equipo. También puedes cargar un dataset de prueba de ecommerce para explorar la herramienta sin necesidad de tener un archivo propio.
- **Resumen estadístico** — Detecta automáticamente columnas numéricas y de texto, y muestra estadísticas por columna: Min, Max, Media, Mediana, valores únicos y nulos. Incluye una leyenda descriptiva de cada métrica.
- **Tabla interactiva** — Búsqueda global, filtros por columna (con confirmación explícita), ordenación por columna y paginación configurable (10, 25, 50 o 100 filas).
- **Mapa de calor** — Las celdas numéricas de la tabla se colorean con tonos pastel según su valor dentro del rango de cada columna (azul para los más bajos, verde-amarillo-naranja para intermedios, rojo para los más altos).
- **Gráficos** — Genera gráficos de barras, líneas, tarta y dispersión (scatter/bubble). Bar y Line soportan hasta 3 variables en el eje Y. Scatter agrega valores por eje X (suma) y soporta variables de texto en los ejes. Bubble permite añadir una tercera variable para definir el diámetro de cada burbuja. Selector de número máximo de puntos para evitar saturación.
- **Exportación** — Descarga los datos filtrados como CSV o el gráfico como imagen PNG.

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
- **Chart.js 4.4.7** — Chart rendering (bar, line, pie, scatter, bubble)

### State Management
A single global `state` object holds all application state:
- `headers` / `rows` — raw parsed data
- `filtered` — rows after applying search + column filters
- `sortCol` / `sortAsc` — current sort state
- `page` — current pagination page
- `colFilters` — per-column filter strings
- `chart` — current Chart.js instance

### Key Functions
- `loadSampleData()` — loads the embedded ecommerce sample CSV and triggers the dashboard
- `loadFile(file)` — parses CSV via PapaParse, populates state, triggers initial render
- `buildSummary()` — computes per-column stats (type detection, min/max/mean/median, unique/null counts)
- `applyFilters()` → `sortData()` → `renderTable()` — the filtering/rendering pipeline; any filter change triggers this chain
- `heatmapColor(t)` — returns a pastel RGB color for a normalized value (0=blue, 1=red) used in table cell backgrounds
- `buildChartControls()` / `renderChart()` — populates axis dropdowns and draws charts; bar/line support up to 3 Y-axis variables; scatter/bubble aggregates by X with text axis support
- `exportCSV()` / `exportChart()` — export filtered data as CSV or chart as PNG

### Column filters
Per-column filters require explicit confirmation (click ✓ button or press Enter). Global search filters on input. Both use case-insensitive substring matching.
