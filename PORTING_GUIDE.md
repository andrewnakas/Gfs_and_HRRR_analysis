# Porting Guide: Multi-Model Weather Analysis Dashboard to Tree60 Weather

This guide provides comprehensive instructions for integrating the Multi-Model Weather Analysis Dashboard into the Tree60 Weather website.

## Table of Contents
1. [Project Overview](#project-overview)
2. [Key Features](#key-features)
3. [Technical Stack](#technical-stack)
4. [File Structure](#file-structure)
5. [Dependencies](#dependencies)
6. [Integration Steps](#integration-steps)
7. [Configuration Options](#configuration-options)
8. [API Usage](#api-usage)
9. [Customization Guide](#customization-guide)
10. [URL Sharing Implementation](#url-sharing-implementation)

---

## Project Overview

This is a comprehensive weather analysis dashboard that supports multiple numerical weather prediction models:
- **GEFS** (Global Ensemble Forecast System) - Global, 0.25°, 3-hour resolution, 2000-present
- **GFS** (Global Forecast System) - Global, 0.25°, 1-hour resolution, 2021-present
- **HRRR** (High-Resolution Rapid Refresh) - Continental US, 3km, 1-hour resolution, 2014-present

The dashboard provides historical weather analysis with interactive maps, date range selection, year-over-year comparison, and shareable URL functionality.

---

## Key Features

### Core Functionality
- **Multi-Model Support**: Toggle between GEFS, GFS, and HRRR models
- **Interactive Map**: Leaflet-based map with location selection and grid visualization
- **Model-Specific Grid Snapping**: Automatically snaps to appropriate grid resolution for each model
- **Date Range Selection**: Flatpickr date pickers with model-specific constraints
- **Year-over-Year Comparison**: Compare current data with historical years
- **URL Sharing**: Generate shareable URLs that encode all configuration parameters
- **Auto-Load**: Shared URLs automatically load and fetch data

### Visualizations
- Temperature Analysis (Max, Mean, Min)
- Precipitation Analysis (Total, Rain, Snowfall)
- Wind Speed Analysis (Max Speed, Gusts)
- Solar Radiation
- Humidity & Pressure
- Cloud Cover & Precipitation Hours
- Cumulative Precipitation & Snow Analysis
- Daily precipitation and snowfall plumes

---

## Technical Stack

### Frontend
- **HTML5**: Semantic markup with responsive design
- **CSS3**: Gradient backgrounds, flexbox/grid layouts, mobile-responsive
- **Vanilla JavaScript**: No framework dependencies

### Libraries (CDN-loaded)
- **Leaflet 1.9.4**: Interactive maps
- **Chart.js 4.4.0**: Data visualization
- **Flatpickr**: Date range picker
- **OpenTopoMap**: Terrain tile layer

### APIs
- **Open-Meteo Archive API**: Historical weather data source
- **Nominatim (OpenStreetMap)**: Reverse geocoding for location names

---

## File Structure

```
Gfs_and_HRRR_analysis/
├── index.html              # Main HTML file with UI structure
├── app.js                  # Core application logic (~1440 lines)
├── README.md               # Project documentation
├── PORTING_GUIDE.md        # This file
├── GITHUB_PAGES_SETUP.md   # GitHub Pages setup instructions
└── .github/
    └── workflows/
        └── deploy.yml      # GitHub Actions deployment workflow
```

---

## Dependencies

All dependencies are loaded via CDN - no npm install or build process required.

### External Resources (from index.html)
```html
<!-- Leaflet CSS & JS -->
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<!-- Chart.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>

<!-- Flatpickr CSS & JS -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
<script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
```

---

## Integration Steps

### Step 1: Copy Core Files
Copy these files to your Tree60 Weather project:
- `index.html` → Rename or integrate into your existing page structure
- `app.js` → Keep as standalone file or integrate into your JS bundle

### Step 2: Update HTML Structure
If integrating into an existing page, extract the key HTML sections:

**Controls Section** (lines 296-329 in index.html):
```html
<div class="controls">
    <!-- Model selector, date pickers, fetch button, share button -->
</div>
```

**Map Container** (lines 331-334):
```html
<div class="map-container">
    <div id="map"></div>
</div>
```

**Charts Section** (lines 343-397):
```html
<div id="charts">
    <!-- 6 weather analysis chart containers -->
</div>
<div id="ensemble-charts">
    <!-- 4 precipitation/snow analysis chart containers -->
</div>
```

### Step 3: Style Integration
Extract CSS from `<style>` tag (lines 14-290) in index.html:
- Copy to your existing CSS file or keep inline
- Adjust colors/gradients to match Tree60 Weather branding
- Modify responsive breakpoints if needed

**Key CSS Classes**:
- `.container` - Main wrapper
- `.controls` - Control panel styling
- `.map-container` - Map wrapper
- `.chart-container` - Individual chart containers
- `.charts-grid` - Grid layout for charts
- `.error`, `.success` - Message displays

### Step 4: JavaScript Integration

**Option A: Standalone Script**
```html
<script src="app.js"></script>
```

**Option B: Module Integration**
If using a bundler, wrap app.js in a module or integrate functions into your existing JS architecture.

**Key JavaScript Functions**:
- `initializeMap()` - Sets up Leaflet map
- `initializeDatePickers()` - Configures Flatpickr
- `fetchWeatherData()` - Main data fetching function
- `renderCharts()` - Creates Chart.js visualizations
- `generateShareURL()` - Creates shareable URL
- `loadStateFromURL()` - Parses URL parameters
- `applyURLState()` - Loads shared configuration

### Step 5: API Configuration
The app uses Open-Meteo Archive API - no API key required.

**API Endpoint Pattern**:
```javascript
const url = `https://archive-api.open-meteo.com/v1/archive?` +
    `latitude=${lat}&longitude=${lon}` +
    `&start_date=${startDate}&end_date=${endDate}` +
    `&daily=temperature_2m_max,temperature_2m_min,...` +
    `&timezone=auto`;
```

### Step 6: Test Integration
1. Verify map loads correctly
2. Test location selection
3. Test date range selection
4. Test data fetching
5. Test chart rendering
6. Test model switching
7. Test URL sharing
8. Test mobile responsiveness

---

## Configuration Options

### Model Configurations (app.js lines 11-47)
```javascript
const MODEL_CONFIGS = {
    gefs: {
        name: 'GEFS',
        gridResolution: 0.25,
        gridUnit: 'degrees',
        temporalResolution: '3 hours',
        minDate: '2000-01-01',
        coverage: 'Global',
        bounds: null
    },
    // ... gfs, hrrr configs
};
```

### Default Location (app.js lines 15-20)
Currently set to Big Sky, Montana (45.2615°N, 111.3081°W).
Change in `DOMContentLoaded` event listener:
```javascript
setTimeout(() => {
    selectLocation(YOUR_LAT, YOUR_LON);
    map.setView([YOUR_LAT, YOUR_LON], ZOOM_LEVEL);
}, 500);
```

### Map Tile Layer (app.js lines 33-37)
Currently using OpenTopoMap. Replace with your preferred tiles:
```javascript
L.tileLayer('YOUR_TILE_URL/{z}/{x}/{y}.png', {
    attribution: 'YOUR_ATTRIBUTION',
    maxZoom: 17
}).addTo(map);
```

### Chart Colors
Colors are defined inline in chart dataset configurations.
Search for `borderColor` and `backgroundColor` in app.js to customize.

---

## API Usage

### Open-Meteo Archive API

**Weather Variables Available**:
- `temperature_2m_max`, `temperature_2m_min`, `temperature_2m_mean`
- `precipitation_sum`, `rain_sum`, `snowfall_sum`
- `windspeed_10m_max`, `windgusts_10m_max`, `winddirection_10m_dominant`
- `shortwave_radiation_sum`
- `relative_humidity_2m_mean`, `surface_pressure_mean`
- `cloudcover_mean`, `precipitation_hours`

**Request Format**:
```javascript
fetch(`https://archive-api.open-meteo.com/v1/archive?latitude=${lat}&longitude=${lon}&start_date=${start}&end_date=${end}&daily=${variables}&timezone=auto`)
```

**Response Format**:
```json
{
    "daily": {
        "time": ["2024-01-01", "2024-01-02", ...],
        "temperature_2m_max": [5.2, 6.1, ...],
        "precipitation_sum": [2.5, 0.0, ...],
        // ... other variables
    }
}
```

**Rate Limits**:
- Free tier: 10,000 requests/day
- No API key required for basic usage

### Nominatim Reverse Geocoding

**Usage** (app.js line 100):
```javascript
fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lon}`)
```

**Rate Limit**:
- 1 request per second
- Include User-Agent header in production

---

## Customization Guide

### Branding Integration

**1. Update Title & Logo**
```html
<h1>🌤️ Tree60 Weather Analysis Dashboard</h1>
```

**2. Color Scheme**
Replace gradient backgrounds:
```css
/* Current purple gradient */
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);

/* Replace with Tree60 Weather colors */
background: linear-gradient(135deg, YOUR_COLOR_1 0%, YOUR_COLOR_2 100%);
```

**3. Button Styling**
Modify button gradients (index.html line 83):
```css
button {
    background: linear-gradient(135deg, YOUR_PRIMARY 0%, YOUR_SECONDARY 100%);
}
```

### Adding New Weather Variables

**1. Add to API Request** (app.js line 205):
```javascript
const url = `https://archive-api.open-meteo.com/v1/archive?` +
    // ... existing params
    `&daily=existing_variables,NEW_VARIABLE` +
    `&timezone=auto`;
```

**2. Add Chart Container** (index.html):
```html
<div class="chart-container">
    <div class="chart-title">Your New Variable</div>
    <canvas id="new-variable-chart"></canvas>
</div>
```

**3. Create Chart** (app.js):
```javascript
charts.newVariable = new Chart(document.getElementById('new-variable-chart'), {
    type: 'line', // or 'bar'
    data: {
        labels: dates,
        datasets: [{
            label: 'New Variable',
            data: data.daily.new_variable,
            borderColor: 'rgb(YOUR_COLOR)',
            // ... styling
        }]
    },
    options: {
        // ... chart options
    }
});
```

### Model Customization

**Add New Model**:
```javascript
const MODEL_CONFIGS = {
    // ... existing models
    newmodel: {
        name: 'NEW_MODEL',
        fullName: 'New Model Full Name',
        gridResolution: 0.5,
        gridUnit: 'degrees',
        temporalResolution: '1 hour',
        minDate: '2020-01-01',
        coverage: 'Global',
        bounds: null // or specify coverage bounds
    }
};
```

Then add option to HTML:
```html
<select id="model-select">
    <!-- existing options -->
    <option value="newmodel">NEW_MODEL (Description)</option>
</select>
```

---

## URL Sharing Implementation

### URL Parameter Structure
```
?model=gefs&lat=45.261500&lon=-111.308100&start=2024-01-01&end=2024-01-31&compare=1&compYear=2023
```

**Parameters**:
- `model`: Weather model identifier (gefs/gfs/hrrr)
- `lat`: Latitude (6 decimal places)
- `lon`: Longitude (6 decimal places)
- `start`: Start date (YYYY-MM-DD)
- `end`: End date (YYYY-MM-DD)
- `compare`: Comparison enabled flag (1 or omitted)
- `compYear`: Comparison year (integer)

### Share Button Handler (app.js lines 1322-1361)
```javascript
function generateShareURL() {
    // Validates location and dates
    // Builds URLSearchParams
    // Copies to clipboard
    // Shows success message
}
```

### URL Loading (app.js lines 1363-1376)
```javascript
function loadStateFromURL() {
    // Parses URL parameters
    // Returns state object or null
}
```

### State Application (app.js lines 1378-1425)
```javascript
function applyURLState(state) {
    // Sets model
    // Sets location
    // Sets dates
    // Sets comparison
    // Auto-fetches data
}
```

### Clipboard API Usage
```javascript
navigator.clipboard.writeText(shareUrl).then(() => {
    showSuccess('URL copied!');
}).catch(err => {
    // Fallback: display URL for manual copy
});
```

---

## Mobile Responsiveness

The dashboard is fully responsive with breakpoints:

**Mobile Portrait** (max-width: 768px):
- Single column layout
- Smaller fonts (9px labels, 11px titles)
- Reduced padding
- Stacked controls

**Mobile Landscape** (max-width: 926px, orientation: landscape):
- Reduced map height (300px)
- Compact charts
- Single column grid

**Tablet Landscape** (927px - 1200px):
- 2-column chart grid
- Standard fonts

**Desktop** (1200px+):
- Auto-fit grid (minmax(500px, 1fr))
- Full-size charts

---

## Performance Considerations

### Chart Rendering
- Charts are destroyed before re-rendering to prevent memory leaks
- `maintainAspectRatio: false` for responsive sizing
- Mobile detection reduces font sizes and point radii

### API Calls
- Single request per date range
- Cached comparison data (if enabled)
- Validation before fetching prevents unnecessary requests

### Map Performance
- Single tile layer
- Grid squares replaced (not accumulated) on location change
- Markers replaced (not accumulated)

---

## Browser Compatibility

**Minimum Requirements**:
- ES6 support (const/let, arrow functions, template literals)
- Fetch API
- URLSearchParams API
- Clipboard API (with fallback)

**Tested Browsers**:
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

**Fallbacks**:
- Clipboard API failure → Display URL for manual copy
- No graceful degradation for ES6 - transpile if IE11 support needed

---

## Deployment Options

### Option 1: Standalone Page
Deploy as separate route in Tree60 Weather:
```
tree60weather.com/analysis
```

### Option 2: Embedded Component
Integrate into existing weather pages as iframe or embedded section.

### Option 3: SPA Integration
If Tree60 Weather is a SPA (React/Vue/Angular):
- Wrap in framework component
- Convert to TypeScript if needed
- Use framework's routing for URL params
- Integrate with existing state management

---

## Testing Checklist

- [ ] Map loads and displays correctly
- [ ] Location selection works (all models)
- [ ] HRRR boundary validation works (rejects non-CONUS locations)
- [ ] Grid snapping displays correct resolution
- [ ] Date pickers respect model constraints
- [ ] Data fetching works for all models
- [ ] Charts render correctly (all 10 charts)
- [ ] Year-over-year comparison works
- [ ] Share button generates URL
- [ ] URL is copied to clipboard
- [ ] Shared URLs load configuration correctly
- [ ] Shared URLs auto-fetch data
- [ ] Mobile layout works (portrait & landscape)
- [ ] Error messages display appropriately
- [ ] Success messages auto-dismiss
- [ ] Reverse geocoding displays location names
- [ ] Charts are responsive to window resize

---

## Troubleshooting

### Common Issues

**Map doesn't load**:
- Check Leaflet CSS/JS CDN links
- Verify container has height set (`#map { height: 500px; }`)

**Charts don't render**:
- Check Chart.js CDN link
- Verify canvas elements exist before rendering
- Check browser console for errors

**Date picker doesn't work**:
- Check Flatpickr CSS/JS CDN links
- Verify input elements have correct IDs

**API returns no data**:
- Check date range is within model availability
- Verify coordinates are valid
- Check browser console for CORS errors

**Shared URLs don't work**:
- Ensure URL parameters are properly encoded
- Check browser console for parsing errors
- Verify auto-fetch timeout is sufficient

---

## Additional Resources

- [Open-Meteo Documentation](https://open-meteo.com/en/docs)
- [Leaflet Documentation](https://leafletjs.com/reference.html)
- [Chart.js Documentation](https://www.chartjs.org/docs/latest/)
- [Flatpickr Documentation](https://flatpickr.js.org/)
- [NOAA GEFS Data Catalog](https://dynamical.org/catalog/noaa-gefs-analysis/)
- [NOAA GFS Data Catalog](https://dynamical.org/catalog/noaa-gfs-analysis/)
- [NOAA HRRR Data Catalog](https://dynamical.org/catalog/noaa-hrrr-analysis/)

---

## License

MIT License - Feel free to modify and integrate into Tree60 Weather.

---

## Support

For questions about this integration, refer to:
- Original repository: https://github.com/andrewnakas/Gfs_and_HRRR_analysis
- Live demo: https://andrewnakas.github.io/Gfs_and_HRRR_analysis/

---

**Last Updated**: 2026-02-28
**Version**: 2.0 (Multi-Model with URL Sharing)
