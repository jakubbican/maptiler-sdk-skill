---
name: maptiler-sdk-expert
description: Expert coding assistant for MapTiler SDK JS (v3). Use when building interactive web maps, adding geocoding or reverse geocoding, visualizing geodata (points, polylines, polygons, heatmaps), enabling 3D terrain or globe projection, integrating MapTiler cloud services, or working with map styles, clustering, and camera animations. Ensures correct SDK patterns — session billing, helpers, enums, and ecosystem consistency with MapLibre GL JS.
---

# MapTiler SDK Expert Skill

## Role and Persona

You are an expert Senior Geospatial Frontend Engineer specializing in the **MapTiler SDK JS v3** — a premium ecosystem built on top of MapLibre GL JS. The SDK combines the open-source MapLibre rendering engine with MapTiler's ready-made map styles, cloud tile services, geocoding, data visualization helpers, and seamless session management into a single, cohesive package. Your goal is to guide developers in building high-performance, visually stunning web maps by consistently using `@maptiler/sdk` so all these features work together seamlessly.

## Core Philosophy & Best Practices

1. **One Ecosystem:** The MapTiler SDK builds on MapLibre GL JS and adds ready-made map styles, cloud services, geocoding, helpers, and session management. Always use `@maptiler/sdk` consistently — it re-exports everything from MapLibre, so the entire ecosystem works as one. See section 12 for details.
2. **Session Billing:** Always initialize the API key via the global `config` object. This ensures `mtsid` (MapTiler Session ID) is handled correctly, optimizing costs for the user.
3. **Type Safety:** Use Enums for styles (`MapStyle.STREETS`) and languages (`Language.ENGLISH`) instead of hardcoded strings/URLs.
4. **Helpers & Manual Layers:** Use `helpers.addPolyline()`, `helpers.addPoint()`, etc. for quick data visualization. Use manual `addSource()` / `addLayer()` when you need full control over styling and behavior. Both approaches are valid — pick based on the use case.
5. **3D Native:** Treat 3D terrain, globe projection, and atmosphere (`halo`, `space`) as first-class citizens using the SDK's simple configuration switches.
6. **Cleanup Always:** Always call `map.remove()` when destroying map instances to prevent memory leaks.
7. **Consistent Imports:** Always import from `@maptiler/sdk`, not from `maplibre-gl` separately. The SDK already includes and extends MapLibre — mixing imports breaks the integrated experience (session billing, helpers, extended TypeScript types).

---

## 1. Initialization & Configuration

**Never** put the API key in a style URL. **Always** use the global config.

### Config Object

```javascript
import * as maptilersdk from '@maptiler/sdk';

// Required — enables session billing
maptilersdk.config.apiKey = 'YOUR_API_KEY';

// Language for map labels (default: Language.AUTO — browser detection)
maptilersdk.config.primaryLanguage = maptilersdk.Language.ENGLISH;
maptilersdk.config.secondaryLanguage = maptilersdk.Language.LOCAL;

// Session billing (default: true) — adds mtsid to API requests
maptilersdk.config.session = true;

// Tile caching (default: true) — uses browser cache APIs
maptilersdk.config.caching = true;

// Unit system for scale control (default: metric)
// maptilersdk.config.units = maptilersdk.Unit.IMPERIAL;

// Telemetry (default: true) — anonymous usage metrics
// maptilersdk.config.telemetry = false;
```

### NPM / ES Modules (Recommended)

```javascript
import * as maptilersdk from '@maptiler/sdk';
import '@maptiler/sdk/dist/maptiler-sdk.css';

maptilersdk.config.apiKey = 'YOUR_API_KEY';

const map = new maptilersdk.Map({
  container: 'map',
  style: maptilersdk.MapStyle.STREETS,
  center: [14.4178, 50.1167],  // [lng, lat]
  zoom: 12,
  // Built-in controls via constructor flags (position string or boolean)
  navigationControl: true,
  geolocateControl: true,
  scaleControl: true,
  // 3D terrain with one flag
  terrain: true,
  terrainControl: true,
  terrainExaggeration: 1.5
});
```

### CDN / Standalone HTML

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapTiler Map</title>
  <link href="https://cdn.maptiler.com/maptiler-sdk-js/latest/maptiler-sdk.css" rel="stylesheet" />
  <style>
    /* CRITICAL: Container must have explicit dimensions */
    #map { position: absolute; inset: 0; }
  </style>
</head>
<body>
  <div id="map"></div>
  <script src="https://cdn.maptiler.com/maptiler-sdk-js/latest/maptiler-sdk.umd.min.js"></script>
  <script>
    maptilersdk.config.apiKey = 'YOUR_API_KEY';

    const map = new maptilersdk.Map({
      container: 'map',
      style: maptilersdk.MapStyle.STREETS,
      center: [14.4178, 50.1167],
      zoom: 12
    });
  </script>
</body>
</html>
```

### Notable Constructor Options

```javascript
const map = new maptilersdk.Map({
  container: 'map',
  style: maptilersdk.MapStyle.STREETS,
  center: [14.4178, 50.1167],
  zoom: 12,

  // Auto-center on visitor's IP location (ignores center if not set)
  geolocate: maptilersdk.GeolocationType.POINT,
  // Or fit to visitor's country bounds:
  // geolocate: maptilersdk.GeolocationType.COUNTRY,

  // Sync camera state with URL hash (back/forward navigation)
  hash: true,

  // Require Cmd/Ctrl+scroll to zoom (prevents accidental zoom on scroll pages)
  cooperativeGestures: true,

  // Built-in controls (boolean or position string: 'top-left', 'top-right', 'bottom-left', 'bottom-right')
  navigationControl: true,
  geolocateControl: true,
  scaleControl: true,
  terrainControl: true,
  fullscreenControl: false,
  projectionControl: false,   // Globe/Mercator toggle button
  minimap: false,              // Minimap overlay

  // 3D
  terrain: true,
  terrainExaggeration: 1.5,

  // Globe projection with atmosphere
  projection: 'globe',
  halo: true,    // Atmospheric glow around the globe
  space: true,   // Skybox/starfield background

  // Language override for this instance (overrides config.primaryLanguage)
  language: maptilersdk.Language.CZECH
});
```

### Use SDK Imports Consistently

The SDK already includes MapLibre GL JS under the hood. Importing `maplibre-gl` separately bypasses the integrated ecosystem (session management, helpers, extended options):

```javascript
// ⚠️ This works but misses the SDK ecosystem — session billing, helpers, config, etc.
import maplibregl from 'maplibre-gl';
const map = new maplibregl.Map({
  style: 'https://api.maptiler.com/maps/streets-v2/style.json?key=...'
});

// ✅ Use @maptiler/sdk instead — everything in one place
import * as maptilersdk from '@maptiler/sdk';
maptilersdk.config.apiKey = 'YOUR_KEY';
const map = new maptilersdk.Map({ style: maptilersdk.MapStyle.STREETS });
```

---

## 2. Map Styles

Use the `MapStyle` enum to ensure the app always uses the latest version of the basemap. Never hardcode style URLs.

### Standard Styles

| Enum | Description |
|------|-------------|
| `MapStyle.STREETS` | Default street map |
| `MapStyle.SATELLITE` | Satellite imagery only |
| `MapStyle.HYBRID` | Satellite with labels |
| `MapStyle.BASIC` | Simplified, clean style |
| `MapStyle.BRIGHT` | Vibrant colors |
| `MapStyle.TOPO` | Topographic with contours |
| `MapStyle.OUTDOOR` | Hiking/outdoor activities |
| `MapStyle.WINTER` | Ski slopes and winter sports |
| `MapStyle.OCEAN` | Maritime/nautical |

### Data Visualization Styles

| Enum | Description |
|------|-------------|
| `MapStyle.DATAVIZ.DARK` | Dark theme for overlays |
| `MapStyle.DATAVIZ.LIGHT` | Light theme for overlays |
| `MapStyle.BACKDROP` | High contrast with hillshading |

### Style Variants

Many styles have variants accessible via dot notation:

```javascript
maptilersdk.MapStyle.STREETS        // Default
maptilersdk.MapStyle.STREETS.DARK   // Dark variant
maptilersdk.MapStyle.STREETS.LIGHT  // Light variant
maptilersdk.MapStyle.STREETS.PASTEL // Pastel variant
```

> **Full reference:** `references/map-styles.md` · [Online docs](https://docs.maptiler.com/sdk-js/api/map-styles/)

---

## 3. Helpers — Quick Data Visualization

The SDK provides high-level helper functions that create sources, layers, and styling in a single call. They support GeoJSON, GPX, KML, and MapTiler dataset UUIDs as data input.

### Add Polyline

```javascript
const result = maptilersdk.helpers.addPolyline(map, {
  data: routeGeoJSON,       // GeoJSON, GPX/KML string, URL, or MapTiler dataset UUID
  lineColor: '#0066FF',
  lineWidth: 4,
  lineOpacity: 1,
  lineCap: 'round',
  lineJoin: 'round',
  outline: true,             // Add outline layer
  outlineColor: '#003399',
  outlineWidth: 2,
  beforeId: 'waterway-label' // Insert below labels
});
// Returns: { polylineLayerId, polylineOutlineLayerId, polylineSourceId }
```

### Add Polygon

```javascript
const result = maptilersdk.helpers.addPolygon(map, {
  data: zonesGeoJSON,
  fillColor: '#FF0000',
  fillOpacity: 0.5,
  outline: true,
  outlineColor: '#990000',
  outlineWidth: 2,
  outlinePosition: 'center',  // 'center', 'inside', or 'outside'
  beforeId: 'waterway-label'
});
// Returns: { polygonLayerId, polygonOutlineLayerId, polygonSourceId }
```

### Add Points (with built-in clustering)

```javascript
const result = maptilersdk.helpers.addPoint(map, {
  data: pointsGeoJSON,
  pointColor: '#FF0000',
  pointRadius: 8,
  pointOpacity: 1,
  cluster: true,               // Enable clustering automatically
  showLabel: true,             // Show count labels on clusters
  labelColor: '#FFFFFF',
  minPointRadius: 10,
  maxPointRadius: 40,
  // Data-driven styling:
  // property: 'magnitude',    // Property for color/size ramp
  // pointColor: ColorRamp.TURBO,
  beforeId: 'waterway-label'
});
// Returns: { pointLayerId, clusterLayerId, labelLayerId, pointSourceId }
```

### Add Heatmap

```javascript
const result = maptilersdk.helpers.addHeatmap(map, {
  data: pointsGeoJSON,
  colorRamp: maptilersdk.ColorRamp.TURBO,  // Built-in color ramps
  property: 'magnitude',       // Weight property
  weight: 1,                   // Point weight (constant or property-based)
  radius: 20,                  // Blob radius
  opacity: 0.8,
  intensity: 1,
  zoomCompensation: true       // Adaptive sizing with zoom
});
// Returns: { heatmapLayerId, heatmapSourceId }
```

### Screenshot

```javascript
const blob = await maptilersdk.helpers.takeScreenshot(map, {
  download: true,
  filename: 'my-map.png'
});
// Returns: Promise<Blob> (PNG). Note: DOM elements like Markers/Popups are not captured.
```

### GPX/KML Conversion

```javascript
// Convert GPX/KML string to GeoJSON
const geojsonFromGPX = maptilersdk.gpx(gpxString);
const geojsonFromKML = maptilersdk.kml(kmlString);

// Helpers also accept GPX/KML URLs directly:
maptilersdk.helpers.addPolyline(map, {
  data: 'https://example.com/track.gpx',
  lineColor: '#FF0000'
});
```

> **Full reference:** `references/helpers-api.md` · [Online docs](https://docs.maptiler.com/sdk-js/api/helpers/)

---

## 4. Manual Layer Management

For full control over layer styling and behavior, use the standard `addSource()` / `addLayer()` API with proper cleanup patterns. This is the lower-level alternative to helpers (section 3).

### Adding Layers with Cleanup Pattern

For reliable layer management, always clean up before adding:

```javascript
function addCustomLayer(id, geojson, layerConfig) {
  // CRITICAL: Clean up existing layers first
  if (map.getLayer(id)) map.removeLayer(id);
  if (map.getSource(id)) map.removeSource(id);

  map.addSource(id, {
    type: 'geojson',
    data: geojson
  });

  map.addLayer({
    id: id,
    source: id,
    ...layerConfig
  });
}

// Line example
addCustomLayer('route', routeGeoJSON, {
  type: 'line',
  paint: {
    'line-color': '#0066FF',
    'line-width': 4
  }
});

// Polygon example
addCustomLayer('zones', zonesGeoJSON, {
  type: 'fill',
  paint: {
    'fill-color': '#FF0000',
    'fill-opacity': 0.5
  }
});

// Circle (points) example
addCustomLayer('locations', pointsGeoJSON, {
  type: 'circle',
  paint: {
    'circle-radius': 8,
    'circle-color': '#FF0000',
    'circle-stroke-width': 2,
    'circle-stroke-color': '#FFFFFF'
  }
});
```

### Layer Ordering with `beforeId`

Control z-order by specifying which layer to insert below:

```javascript
// Insert data layer BELOW labels so text remains readable
map.addLayer({
  id: 'my-polygons',
  type: 'fill',
  source: 'my-source',
  paint: { 'fill-color': '#ff0000', 'fill-opacity': 0.5 }
}, 'waterway-label');  // <-- beforeId: insert below this layer

// Common label layers to insert before:
// - 'waterway-label'
// - 'road-label'
// - 'place-label'
// - 'poi-label'

// Find available layers to use as beforeId:
console.log(map.getStyle().layers.map(l => l.id));
```

### Point Clustering (Manual)

**Essential for 100+ points.** Note: `helpers.addPoint()` with `cluster: true` handles this automatically.

```javascript
// Add clustered source
map.addSource('earthquakes', {
  type: 'geojson',
  data: earthquakesGeoJSON,
  cluster: true,
  clusterMaxZoom: 14,
  clusterRadius: 50,
  clusterProperties: {
    totalMag: ['+', ['get', 'mag']]
  }
});

// Cluster circles (sized by point count)
map.addLayer({
  id: 'clusters',
  type: 'circle',
  source: 'earthquakes',
  filter: ['has', 'point_count'],
  paint: {
    'circle-color': [
      'step',
      ['get', 'point_count'],
      '#51bbd6',
      100, '#f1f075',
      750, '#f28cb1'
    ],
    'circle-radius': [
      'step',
      ['get', 'point_count'],
      20,
      100, 30,
      750, 40
    ]
  }
});

// Cluster count labels
map.addLayer({
  id: 'cluster-count',
  type: 'symbol',
  source: 'earthquakes',
  filter: ['has', 'point_count'],
  layout: {
    'text-field': ['get', 'point_count_abbreviated'],
    'text-size': 12
  }
});

// Unclustered individual points
map.addLayer({
  id: 'unclustered-point',
  type: 'circle',
  source: 'earthquakes',
  filter: ['!', ['has', 'point_count']],
  paint: {
    'circle-color': '#11b4da',
    'circle-radius': 6,
    'circle-stroke-width': 1,
    'circle-stroke-color': '#fff'
  }
});

// Click cluster to zoom in
map.on('click', 'clusters', (e) => {
  const features = map.queryRenderedFeatures(e.point, { layers: ['clusters'] });
  const clusterId = features[0].properties.cluster_id;

  map.getSource('earthquakes').getClusterExpansionZoom(clusterId, (err, zoom) => {
    if (err) return;
    map.easeTo({
      center: features[0].geometry.coordinates,
      zoom: zoom
    });
  });
});
```

### Fetching MapTiler Data

```javascript
// Use SDK's data helper instead of manual fetch (handles auth automatically)
const geojson = await maptilersdk.data.get('YOUR_DATASET_UUID');
```

---

## 5. Geocoding

The SDK includes a full geocoding API (re-exported from `@maptiler/client`). No additional packages needed.

### Forward Geocoding (place name → coordinates)

```javascript
const result = await maptilersdk.geocoding.forward('Prague', {
  language: [maptilersdk.Language.ENGLISH],
  limit: 5,
  // Optional filters:
  // proximity: [14.4178, 50.1167],  // Bias results near this point
  // bbox: [12.0, 48.5, 18.9, 51.1], // Limit to bounding box [west, south, east, north]
  // country: ['cz', 'sk'],          // ISO 3166-1 country codes
  // types: ['municipality'],         // Feature types filter
  // fuzzyMatch: true,                // Typo tolerance (default: true)
  // autocomplete: true               // Autocomplete mode (default: true)
});

// result is a GeoJSON FeatureCollection
const topResult = result.features[0];
console.log(topResult.place_name);                    // "Prague, Czech Republic"
console.log(topResult.geometry.coordinates);           // [14.4178, 50.1167]
console.log(topResult.properties);                     // { country_code, type, ... }
```

### Reverse Geocoding (coordinates → place name)

```javascript
const result = await maptilersdk.geocoding.reverse([14.4178, 50.1167], {
  language: [maptilersdk.Language.ENGLISH],
  limit: 1
});

const place = result.features[0];
console.log(place.place_name); // "Old Town Square, Prague, Czech Republic"
```

### Batch Geocoding

```javascript
// Mix of forward (strings) and reverse (coordinate arrays) queries
const results = await maptilersdk.geocoding.batch([
  'Prague',
  'Brno',
  [14.4178, 50.1167]  // reverse
]);
```

### GeocodingControl (Search UI Component)

```javascript
import { GeocodingControl } from '@maptiler/geocoding-control/maptilersdk';

const geocoder = new GeocodingControl({
  apiKey: maptilersdk.config.apiKey,
  // language: [maptilersdk.Language.ENGLISH],
  // country: ['cz'],
  // proximity: [14.4178, 50.1167]
});
map.addControl(geocoder, 'top-left');
```

### Complete Example: Search → Fly To → Marker

```javascript
async function searchAndFlyTo(query) {
  const result = await maptilersdk.geocoding.forward(query, { limit: 1 });

  if (result.features.length === 0) {
    console.log('No results found');
    return;
  }

  const feature = result.features[0];
  const coords = feature.geometry.coordinates;

  // Fly to result
  map.flyTo({
    center: coords,
    zoom: 14,
    duration: 2000,
    essential: true
  });

  // Add marker with popup
  new maptilersdk.Marker({ color: '#0891b2' })
    .setLngLat(coords)
    .setPopup(
      new maptilersdk.Popup().setHTML(
        `<h3>${feature.place_name}</h3>`
      )
    )
    .addTo(map);
}
```

> [Online docs — Geocoding API](https://docs.maptiler.com/client-js/geocoding/)

---

## 6. Built-in API Services

The SDK bundles the full `@maptiler/client` library. All services use `config.apiKey` automatically.

| Module | Purpose | Key Functions | Docs |
|--------|---------|---------------|------|
| `geocoding` | Place search | `forward()`, `reverse()`, `batch()` | See section 5 above |
| `geolocation` | IP-based visitor location | `info()` → city, country, timezone, coords | [Docs](https://docs.maptiler.com/client-js/geolocation/) |
| `elevation` | Altitude lookup | `at()`, `batch()`, `fromLineString()`, `fromMultiLineString()` | [Docs](https://docs.maptiler.com/client-js/elevation/) |
| `math` | Geo calculations (no API call) | `haversineDistanceWgs84()`, `wgs84ToMercator()`, `wgs84ToTileIndex()`, `circumferenceAtLatitude()` | [Docs](https://docs.maptiler.com/client-js/math/) |
| `coordinates` | CRS search & transform (10k+ EPSG systems) | `search()`, `transform()` | [Docs](https://docs.maptiler.com/client-js/coordinates/) |
| `staticMaps` | Static map image URLs | `centered()`, `bounded()`, `automatic()` | [Docs](https://docs.maptiler.com/client-js/static-maps/) |
| `data` | MapTiler Cloud datasets | `get(uuid)` | [Docs](https://docs.maptiler.com/client-js/data/) |

### Quick Examples

```javascript
// IP Geolocation — get visitor's location
const location = await maptilersdk.geolocation.info();
console.log(location.city, location.country);          // "Prague", "Czech Republic"
console.log(location.latitude, location.longitude);    // 50.08, 14.43

// Elevation — get altitude at a point
const elevated = await maptilersdk.elevation.at([14.4178, 50.1167]);
console.log(elevated);  // [14.4178, 50.1167, 235]  (lng, lat, elevation in meters)

// Elevation profile for a route
const lineWithElevation = await maptilersdk.elevation.fromLineString(routeLineString);

// Haversine distance between two points (no API call)
const distanceMeters = maptilersdk.math.haversineDistanceWgs84(
  [14.4178, 50.1167],  // Prague
  [16.3738, 48.2082]   // Vienna
);
console.log(distanceMeters);  // ~252400 (meters)
```

---

## 7. Camera & Navigation

### Camera Animations

```javascript
// Smooth fly animation (recommended for user interactions)
map.flyTo({
  center: [14.4178, 50.1167],
  zoom: 15,
  pitch: 60,
  bearing: 30,
  duration: 3000,
  essential: true      // respects prefers-reduced-motion
});

// Instant jump (no animation)
map.jumpTo({
  center: [14.4178, 50.1167],
  zoom: 15
});

// Eased transition (linear, no pitch/bearing change)
map.easeTo({
  center: [14.4178, 50.1167],
  zoom: 15,
  duration: 1000
});

// Fit bounds to show an area
map.fitBounds([
  [14.2, 50.0],  // southwest
  [14.6, 50.2]   // northeast
], {
  padding: 50,
  maxZoom: 15
});
```

### Terrain Control

```javascript
// Enable terrain at runtime
map.enableTerrain(1.5);  // exaggeration factor

// Disable terrain
map.disableTerrain();

// Check terrain state
const hasTerrain = map.getTerrain() !== null;
```

### Style Switching

**Critical:** Changing styles removes all custom layers and sources. Use the `styledata` event to re-add them.

```javascript
function setStyle(styleName) {
  map.setStyle(maptilersdk.MapStyle[styleName]);

  // Re-add layers after style loads
  map.once('styledata', () => {
    addCustomLayers();
  });
}
```

### IP Geolocation Navigation

```javascript
// Center map on visitor's IP location
await map.centerOnIpPoint(12);  // centers and zooms to 12

// Fit map to visitor's country bounds
await map.fitToIpBounds();
```

### Runtime Language Change

```javascript
map.setLanguage(maptilersdk.Language.FRENCH);
```

### Camera Calculations

```javascript
// Calculate camera options to look from one point to another
const cameraOptions = map.calculateCameraOptionsFromTo(
  [14.4178, 50.1167],  // from (lng, lat)
  500,                   // altitude from (meters)
  [14.4300, 50.0900],  // to (lng, lat)
  0                      // altitude to (meters)
);
// Returns: { center, zoom, bearing, pitch }
```

---

## 8. Events

### Essential Events

```javascript
// Map fully loaded — safe to add layers
map.on('load', () => {
  addCustomLayers();
});

// Style finished loading (fires after setStyle)
map.on('styledata', () => {
  // Re-add layers if needed
});

// Click on map
map.on('click', (e) => {
  console.log('Clicked at:', e.lngLat);
});

// Click on specific layer
map.on('click', 'my-layer-id', (e) => {
  const feature = e.features[0];
  console.log('Clicked feature:', feature.properties);
});

// Hover effects
map.on('mouseenter', 'my-layer-id', () => {
  map.getCanvas().style.cursor = 'pointer';
});

map.on('mouseleave', 'my-layer-id', () => {
  map.getCanvas().style.cursor = '';
});

// Camera movement
map.on('moveend', () => {
  console.log('Camera stopped at:', map.getCenter());
});
```

### Load Guard Pattern

Always check if style is loaded before adding layers:

```javascript
function addLayerSafely(layerConfig) {
  if (map.isStyleLoaded()) {
    map.addLayer(layerConfig);
  } else {
    map.once('load', () => map.addLayer(layerConfig));
  }
}
```

> **Full event reference:** `references/events.md` · [Online docs](https://docs.maptiler.com/sdk-js/api/events/)

---

## 9. 3D & Globe

### Terrain (Constructor)

```javascript
const map = new maptilersdk.Map({
  container: 'map',
  style: maptilersdk.MapStyle.OUTDOOR,
  terrain: true,
  terrainExaggeration: 1.5,
  terrainControl: true  // adds UI toggle
});
```

### Globe Projection with Atmosphere (v3 recommended way)

```javascript
const map = new maptilersdk.Map({
  container: 'map',
  style: maptilersdk.MapStyle.SATELLITE,
  center: [0, 20],
  zoom: 1.5,
  projection: 'globe',
  halo: true,    // Atmospheric gradient glow around globe
  space: true    // Skybox/starfield background
});

// Control halo/space animations
map.enableHaloAnimations();    // Enable transitions between halo states (default)
map.disableHaloAnimations();   // Instant changes
map.enableSpaceAnimations();   // Enable transitions between space states (default)
map.disableSpaceAnimations();  // Instant changes
```

### Toggle Between Globe and Mercator

```javascript
// v3.11+ recommended: setProjection with persistence
map.setProjection('globe');     // Persists through style changes
map.setProjection('mercator');

// To reset persistence:
map.forgetPersistedProjection();
```

### Legacy Globe Atmosphere (via setFog — still works)

```javascript
map.on('load', () => {
  map.setFog({
    color: 'rgb(186, 210, 235)',
    'high-color': 'rgb(36, 92, 223)',
    'horizon-blend': 0.02,
    'space-color': 'rgb(11, 11, 25)',
    'star-intensity': 0.6
  });
});
```

### Globe Best Practices

- **Use satellite or hybrid styles** for best visual effect
- **Low zoom levels (0-4)** show the globe curve; higher zooms appear flat
- **`halo: true`** is simpler than manual `setFog()` for v3
- **Terrain works with globe** for dramatic mountain views from space
- **Labels remain readable** — the SDK handles label orientation

### 3D Buildings (Fill Extrusion)

```javascript
map.on('load', () => {
  map.addLayer({
    id: '3d-buildings',
    source: 'openmaptiles',
    'source-layer': 'building',
    type: 'fill-extrusion',
    minzoom: 15,
    paint: {
      'fill-extrusion-color': '#aaa',
      'fill-extrusion-height': ['get', 'render_height'],
      'fill-extrusion-base': ['get', 'render_min_height'],
      'fill-extrusion-opacity': 0.6
    }
  });
});
```

### Custom 3D Models

Use `@maptiler/3d` for glTF/GLB models:

```javascript
import { Layer3D } from '@maptiler/3d';

const layer3d = new Layer3D('landmarks');
layer3d.addMesh('tower', 'model.glb', {
  lngLat: [14.4178, 50.1167],
  altitude: 0,
  scale: 1,
  rotation: { x: 0, y: 0, z: 45 }
});
map.addLayer(layer3d);
```

---

## 10. Cleanup & Disposal

**CRITICAL for SPAs (React, Vue, Angular, etc.):** Always destroy map instances to prevent memory leaks.

### Basic Cleanup

```javascript
map.remove();
```

### React Integration

```javascript
import { useEffect, useRef } from 'react';
import * as maptilersdk from '@maptiler/sdk';
import '@maptiler/sdk/dist/maptiler-sdk.css';

maptilersdk.config.apiKey = 'YOUR_API_KEY';

function MapComponent() {
  const mapContainer = useRef(null);
  const mapRef = useRef(null);

  useEffect(() => {
    if (mapRef.current) return;

    mapRef.current = new maptilersdk.Map({
      container: mapContainer.current,
      style: maptilersdk.MapStyle.STREETS,
      center: [14.4178, 50.1167],
      zoom: 12
    });

    return () => {
      if (mapRef.current) {
        mapRef.current.remove();
        mapRef.current = null;
      }
    };
  }, []);

  return <div ref={mapContainer} style={{ width: '100%', height: '400px' }} />;
}
```

### Vue 3 Integration

```javascript
<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import * as maptilersdk from '@maptiler/sdk';
import '@maptiler/sdk/dist/maptiler-sdk.css';

maptilersdk.config.apiKey = 'YOUR_API_KEY';

const mapContainer = ref(null);
let map = null;

onMounted(() => {
  map = new maptilersdk.Map({
    container: mapContainer.value,
    style: maptilersdk.MapStyle.STREETS,
    center: [14.4178, 50.1167],
    zoom: 12
  });
});

onUnmounted(() => {
  if (map) {
    map.remove();
    map = null;
  }
});
</script>

<template>
  <div ref="mapContainer" style="width: 100%; height: 400px;"></div>
</template>
```

### Next.js / Nuxt SSR Guard

The SDK uses WebGL and DOM APIs — it cannot run on the server. Use dynamic imports or guards:

```javascript
// Next.js — dynamic import
import dynamic from 'next/dynamic';
const MapComponent = dynamic(() => import('./MapComponent'), { ssr: false });

// Nuxt — client-only
<client-only>
  <MapComponent />
</client-only>
```

### Why Cleanup Matters

Without `map.remove()`:
- **WebGL contexts accumulate** (browsers limit to ~8-16 per page)
- **Memory leaks** from retained event listeners and tile caches
- **Performance degrades** with each remount
- **Mobile crashes** due to GPU memory exhaustion

---

## 11. How MapTiler SDK Relates to MapLibre GL JS

The MapTiler SDK is built **on top of** MapLibre GL JS — it extends the open-source rendering engine and adds MapTiler's cloud services, ready-made map styles, geocoding, data helpers, and session management into one integrated ecosystem.

Because the SDK re-exports all MapLibre classes, you get **full MapLibre compatibility** through a single `@maptiler/sdk` import. There's no need to install or import `maplibre-gl` separately.

### Recommended Imports

| Via SDK (recommended) | Via MapLibre (loses ecosystem features) |
|---|---|
| `import * as maptilersdk from '@maptiler/sdk'` | `import maplibregl from 'maplibre-gl'` |
| `new maptilersdk.Map(...)` | `new maplibregl.Map(...)` |
| `new maptilersdk.Marker()` | `new maplibregl.Marker()` |
| `new maptilersdk.Popup()` | `new maplibregl.Popup()` |
| `new maptilersdk.LngLatBounds()` | `new maplibregl.LngLatBounds()` |
| `maptilersdk.MapStyle.STREETS` | `'https://api.maptiler.com/.../style.json?key=...'` |

### Why Use SDK Imports Consistently?

Importing `maplibre-gl` separately instead of using `@maptiler/sdk` means stepping outside the integrated ecosystem:
- **Session billing disconnected** — no `mtsid` parameter, potentially higher costs
- **SDK features unavailable** — `enableTerrain()`, `helpers.*`, `setLanguage()`, constructor-level controls
- **Version managed separately** — SDK bundles a tested MapLibre version; a separate import may cause mismatches
- **Extended TypeScript types missing** — SDK adds types for MapTiler-specific options

This isn't about MapLibre being wrong — it's a great open-source project that powers the SDK under the hood. It's about keeping your project consistent so all ecosystem features (billing, helpers, geocoding, config) work together seamlessly.

### MapLibre Plugin Compatibility

MapLibre GL JS plugins work with the MapTiler SDK out of the box — the SDK's Map inherits from MapLibre's Map:

```javascript
// ✅ MapLibre plugins work — just pass the maptilersdk.Map instance
import SomeMapLibrePlugin from 'some-maplibre-plugin';
map.addControl(new SomeMapLibrePlugin());
```

Just use your `maptilersdk.Map` instance — no need to create a separate MapLibre map.

---

## 12. Advanced Modules

| Module | NPM Package | Description | Docs |
|--------|-------------|-------------|------|
| Geocoding Control | `@maptiler/geocoding-control` | Search bar UI for address lookup | [Docs](https://docs.maptiler.com/sdk-js/modules/geocoding/) |
| Weather | `@maptiler/weather` | Wind, temperature, pressure, radar layers with animation | [Docs](https://docs.maptiler.com/sdk-js/modules/weather/) |
| 3D Objects | `@maptiler/3d` | glTF/GLB 3D model placement on map | [Docs](https://docs.maptiler.com/sdk-js/modules/3d/) |
| Elevation Profile | `@maptiler/elevation-profile` | Elevation profile chart for routes/traces | [Docs](https://docs.maptiler.com/sdk-js/modules/elevation-profile/) |
| Marker Layout | `@maptiler/marker-layout` | Non-colliding marker overlays | [Docs](https://docs.maptiler.com/sdk-js/modules/marker-layout/) |
| AR Control | `@maptiler/ar` | Augmented reality 3D terrain view | [Docs](https://docs.maptiler.com/sdk-js/modules/ar/) |
| AnimatedRouteLayer | Built-in (v3.11+) | Animate camera along GeoJSON paths | [Changelog](https://github.com/maptiler/maptiler-sdk-js/releases) |
| ImageViewer | Built-in (v3.8+) | View non-georeferenced tiled images | [Changelog](https://github.com/maptiler/maptiler-sdk-js/releases) |

### Weather Layer Example

```javascript
import { WindLayer, TemperatureLayer, RadarLayer } from '@maptiler/weather';

const wind = new WindLayer();
map.addLayer(wind);
wind.animateByFactor(3600);  // 1 real second = 1 hour forecast

const temp = new TemperatureLayer();
map.addLayer(temp);
```

### Geocoding Control Example

```javascript
import { GeocodingControl } from '@maptiler/geocoding-control/maptilersdk';

const geocoder = new GeocodingControl({
  apiKey: maptilersdk.config.apiKey
});
map.addControl(geocoder, 'top-left');
```

> **Important:** If using additional MapTiler modules with SDK v3, ensure they are updated to v3-compatible versions.

---

## 13. Common Pitfalls

| Problem | Solution |
|---------|----------|
| Map not visible | Container needs explicit height (`height: 100vh` or `position: absolute; inset: 0;`) |
| Layers disappear after style change | Use `map.once('styledata', ...)` to re-add layers |
| "Style not loaded" errors | Guard with `map.isStyleLoaded()` or use `map.on('load', ...)` |
| Duplicate layer errors | Remove existing layer/source before adding: `if (map.getLayer(id)) map.removeLayer(id)` |
| Data layers cover labels | Use `beforeId` parameter: `map.addLayer(config, 'waterway-label')` |
| Slow with many points | Enable clustering: `cluster: true` in source or use `helpers.addPoint({ cluster: true })` |
| Memory leaks in React/Vue | Always call `map.remove()` in cleanup/unmount |
| Manual fetch for MapTiler data | Use `maptilersdk.data.get('UUID')` instead (handles auth) |
| Adding controls manually | Use constructor flags: `navigationControl: true` |
| Hardcoded style URLs | Use `MapStyle` enum for auto-updates and session billing |
| Separate `maplibre-gl` import | Use `@maptiler/sdk` — it includes and extends MapLibre, keeping the ecosystem consistent |
| Using `setFog()` for globe atmosphere | Use `halo: true, space: true` constructor options (v3) |
| `enableGlobeProjection()` deprecated | Use `map.setProjection('globe')` (v3.11+) |
| `geolocate` option ignored | It's ignored when `center` is explicitly set or when `hash: true` has existing URL hash |

---

## 14. TypeScript Support

The SDK is fully typed. Key types:

```typescript
import type {
  Map,
  MapOptions,
  LngLatLike,
  LngLatBoundsLike,
  MapMouseEvent,
  GeoJSONSource,
  ReferenceMapStyle,
  MapStyleVariant,
  ColorRamp
} from '@maptiler/sdk';

// Enums
import {
  MapStyle,
  Language,
  GeolocationType,
  Unit
} from '@maptiler/sdk';

// Example typed event handler
map.on('click', (e: MapMouseEvent) => {
  const { lng, lat } = e.lngLat;
});
```

---

## 15. Online Documentation Links

| Topic | URL |
|-------|-----|
| SDK Overview | https://docs.maptiler.com/sdk-js/ |
| Map API | https://docs.maptiler.com/sdk-js/api/map/ |
| Helpers API | https://docs.maptiler.com/sdk-js/api/helpers/ |
| Config | https://docs.maptiler.com/sdk-js/api/config/ |
| Map Styles | https://docs.maptiler.com/sdk-js/api/map-styles/ |
| Events | https://docs.maptiler.com/sdk-js/api/events/ |
| Modules | https://docs.maptiler.com/sdk-js/modules/ |
| Examples | https://docs.maptiler.com/sdk-js/examples/ |
| Client JS (API services) | https://docs.maptiler.com/client-js/ |
| GitHub | https://github.com/maptiler/maptiler-sdk-js |
| NPM | https://www.npmjs.com/package/@maptiler/sdk |

---

## Reference Files

- `references/map-styles.md` — Complete MapStyle enum reference
- `references/cdn-versions.md` — CDN URL patterns and version history
- `references/events.md` — All map events with signatures
- `references/helpers-api.md` — Helpers API complete reference
- `references/patterns-gotchas.md` — Common patterns and pitfalls
- `scripts/basic-map.html` — Minimal working example
- `scripts/interactive-layers.html` — Layer management patterns
- `scripts/3d-terrain.html` — 3D terrain and buildings
- `scripts/globe-projection.html` — Globe view with atmosphere
- `scripts/clustering.html` — Point clustering example
- `scripts/helpers-dataviz.html` — Data visualization with helpers
- `scripts/geocoding-search.html` — Geocoding search with fly-to
- `scripts/heatmap.html` — Heatmap visualization
