# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**WetterSymbol** is a real-time weather station dashboard that displays current weather conditions and forecasts. It's a single-page application built with vanilla JavaScript, HTML, and CSS—no frameworks or build tools required.

### Architecture

- **Single File**: All code lives in `index.html` (HTML structure, inline CSS, inline JavaScript)
- **No Build Process**: The file can be opened directly in a browser
- **No Dependencies**: No npm packages or external libraries (except public APIs)
- **API Data Source**: [Open-Meteo](https://open-meteo.com/) for weather data (free, no auth required)
- **Reverse Geocoding**: Third-party geocoding API for location names
- **localStorage**: Persists theme preference and language selection
- **Browser APIs Used**: Geolocation API, fetch API, SVG for charts/visualizations

### Core Features

1. **Weather Display**: Current conditions, temperature, feels-like, dew point, apparent temperature
2. **Detailed Metrics**: Humidity, pressure, cloud cover, visibility, UV index, precipitation
3. **Wind Data**: Wind speed, direction, and gusts displayed with a compass visualization
4. **Charts**: 24-hour temperature and precipitation trends rendered as inline SVG
5. **Theme Support**: Light and dark mode toggle (persisted in localStorage)
6. **Localization**: English, German, French, Spanish, plus additional languages (i18n via `LANGS` object)
7. **Location Selection**:
   - Automatic geolocation detection
   - Manual location picker with country/region browser
   - Fallback to default location if geolocation denied
8. **Responsive Design**: Grid-based layout that adapts to mobile, tablet, and desktop

## Key Functions

### Data Fetching & Initialization

- **`init(lat, lon, fallback, fixedName)`**: Main entry point. Handles geolocation, fetches weather, renders UI
- **`fetchWeather(lat, lon)`**: Calls Open-Meteo API for weather data. Returns structured data object with current conditions and hourly/daily forecasts
- **`geocode(lat, lon)`**: Calls reverse geocoding API to get human-readable location name from coordinates

### Rendering

- **`render(data, locName, lat, lon)`**: Main rendering function. Takes weather data and updates the entire UI with all cards, charts, and metrics. Respects the current theme (light/dark)

### UI Interaction

- **`toggleCountry(ci, panelId)`**: Expands/collapses country sections in location picker
- **`openLocMenuPanel(btnEl)`**: Opens the floating location selection menu
- **`closeLocMenu()`**: Closes the floating menu
- Theme toggle is handled via a click listener on `#theme-btn`
- Language selection triggers a re-render with new locale

## Theming & i18n

- **Theme System**: CSS custom properties (CSS variables) in `:root`. Dark theme is default. Light mode switches all color variables via `data-theme="light"` on `<html>` element
  - Light mode colors are defined in a media query near the top of the CSS
  - Theme state is saved to `localStorage` under key `'meteo-theme'`
- **Language System**: Global `LANGS` object maps language codes to translation objects. The `t(key)` function retrieves translations, falling back to English
  - Language preference is stored in `localStorage` under key `'meteo-lang'`
  - Currently supports: English (`en`), German (`de`), French (`fr`), Spanish (`es`), and others

## Layout & Components

- **Header**: Displays brand logo, current time, location name, and coordinates
- **Hero Section**: Large temperature display, current condition emoji/icon, feels-like temp, and wind compass
- **Metrics Grid**: 4-column grid (responsive to 2 columns on mobile) showing humidity, pressure, cloud cover, visibility, UV index, precipitation, etc.
- **Charts Section**: SVG-based line charts showing 24-hour temperature and precipitation trends
- **Sections**: Organized with visual dividers (horizontal rule + section title dot)

## SVG Chart Generation

Charts are generated as inline SVG (not using a charting library):

- Temperature chart interpolates hourly data points
- Precipitation chart displays rain/snow amounts
- Charts are responsive and scale to container width
- Y-axis scales dynamically based on data range

## Data Structure from Open-Meteo API

The `fetchWeather()` function returns an object with:

- `current`: Current temperature, condition (WMO code), wind speed/direction, humidity, pressure, etc.
- `hourly`: Arrays of hourly data (temperature, precipitation, cloud cover, etc.) for 24 hours
- `daily`: Daily forecast data (max/min temp, precipitation, snow, etc.)

## Common Development Tasks

### Adding a New Metric Card

1. Add new key to the `LANGS` object for all supported languages (translation key)
2. Locate the metrics grid HTML structure (around line 600-700)
3. Add a new `<div class="metric-card">` with the appropriate structure
4. In the `render()` function, find where metrics are populated and add logic to set the card's content from the weather data object

### Adding a New Language

1. Add a new language object to `LANGS` with the same keys as existing languages
2. The `t(key)` function will automatically support it
3. If you need to add UI for language selection, update the location menu

### Changing Colors/Theme

- **Dark mode**: Modify CSS custom properties in `:root` (lines 14-28)
- **Light mode**: Modify the media query or the light theme color variables (search for `data-theme="light"` in styles)
- All colors are centralized as CSS variables, making bulk changes easy

### Testing a Location

1. Open the browser console and run: `init(LATITUDE, LONGITUDE, true, 'City Name')`
2. Or use the location picker to select a location manually

## Important Notes

- **No type checking**: This is vanilla JavaScript with no TypeScript or JSDoc
- **Global state**: Variables like `lastRenderArgs`, `lastLocationIndex`, and `currentLang` are global
- **Responsive breakpoints**: 900px and 720px media queries handle tablet and mobile layouts
- **Wind compass**: Generated programmatically as SVG with cardinal direction labels
- **Time display**: Uses browser's local time (not server time)
- **API Limitations**: Open-Meteo is rate-limited but very generous for public use. No authentication required
