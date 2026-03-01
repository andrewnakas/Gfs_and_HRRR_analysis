# Multi-Model Weather Analysis Dashboard

A comprehensive weather analysis dashboard supporting multiple numerical weather prediction models: GEFS, GFS, and HRRR. Built with Leaflet maps and Chart.js visualizations, this tool allows you to analyze historical weather data with detailed charts for temperature, precipitation, wind, solar radiation, and more.

## Supported Models

### GEFS (Global Ensemble Forecast System)
- **Coverage**: Global
- **Resolution**: 0.25° (~20km)
- **Temporal**: 3-hour data
- **Available from**: 2000-01-01 to present
- **Data catalog**: [NOAA GEFS Analysis](https://dynamical.org/catalog/noaa-gefs-analysis/)

### GFS (Global Forecast System)
- **Coverage**: Global
- **Resolution**: 0.25° (~20km)
- **Temporal**: 1-hour data
- **Available from**: 2021-05-01 to present
- **Data catalog**: [NOAA GFS Analysis](https://dynamical.org/catalog/noaa-gfs-analysis/)

### HRRR (High-Resolution Rapid Refresh)
- **Coverage**: Continental United States
- **Resolution**: 3km
- **Temporal**: 1-hour data
- **Available from**: 2014-10-01 to present (with gaps before 2018)
- **Data catalog**: [NOAA HRRR Analysis](https://dynamical.org/catalog/noaa-hrrr-analysis/)

## Features

- **Multi-Model Support**: Switch between GEFS, GFS, and HRRR models
- **Interactive Map**: Click anywhere on the map to select your location (model coverage applies)
- **Model-Specific Grid Snapping**: Automatically snaps to the appropriate grid resolution
- **Flexible Date Range**: Select historical date ranges appropriate for each model
- **Year-over-Year Comparison**: Compare current data with previous years
- **Comprehensive Weather Charts**:
  - Temperature Analysis (Max, Mean, Min)
  - Precipitation Analysis (Total, Rain, Snowfall)
  - Wind Speed Analysis (Max Speed, Gusts)
  - Solar Radiation
  - Humidity & Pressure
  - Cloud Cover & Precipitation Hours
  - Cumulative Precipitation & Snow Analysis

## Data Sources

- Historical weather data provided by [Open-Meteo](https://open-meteo.com/)
- Model specifications from [Dynamical.org](https://dynamical.org/) data catalogs

## Technologies Used

- **Leaflet** - Interactive maps
- **Chart.js** - Weather data visualization
- **Flatpickr** - Date range picker
- **Open-Meteo API** - Historical weather data

## Live Demo

Visit the [live demo](https://andrewnakas.github.io/Gfs_and_HRRR_analysis/) to try it out!

## How to Use

1. Select a weather model (GEFS, GFS, or HRRR) from the dropdown
2. Click on any location on the map to select it (within model coverage area)
3. Choose your start and end dates (constrained by model availability)
4. Optionally enable year-over-year comparison and select a comparison year
5. Click "Fetch Weather Data" to load and visualize the weather information
6. Scroll down to view all the weather analysis charts

## Local Development

Simply open `index.html` in a web browser - no build process required!

## License

MIT
