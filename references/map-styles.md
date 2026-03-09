# MapTiler SDK — Map Styles Reference

Complete reference for all available `MapStyle` enum values in the MapTiler SDK v3.

> [Online docs](https://docs.maptiler.com/sdk-js/api/map-styles/)

## Usage

```javascript
import * as maptilersdk from '@maptiler/sdk';

const map = new maptilersdk.Map({
  style: maptilersdk.MapStyle.STREETS
});

// Change style at runtime
map.setStyle(maptilersdk.MapStyle.SATELLITE);
```

---

## Standard Styles

### STREETS
Default street map with roads, labels, and POIs.
```javascript
MapStyle.STREETS
MapStyle.STREETS.DARK
MapStyle.STREETS.LIGHT
MapStyle.STREETS.PASTEL
```

### SATELLITE
Satellite/aerial imagery without labels.
```javascript
MapStyle.SATELLITE
```

### HYBRID
Satellite imagery with street labels and roads overlay.
```javascript
MapStyle.HYBRID
```

### BASIC
Simplified, minimal style with essential features.
```javascript
MapStyle.BASIC
MapStyle.BASIC.DARK
MapStyle.BASIC.LIGHT
```

### BRIGHT
Vibrant, colorful street map.
```javascript
MapStyle.BRIGHT
MapStyle.BRIGHT.DARK
MapStyle.BRIGHT.LIGHT
MapStyle.BRIGHT.PASTEL
```

### TOPO
Topographic map with elevation contours.
```javascript
MapStyle.TOPO
```

---

## Specialized Styles

### OUTDOOR
Optimized for hiking, cycling, and outdoor activities. Shows trails, elevation, terrain features.
```javascript
MapStyle.OUTDOOR
```

### WINTER
Winter sports style with ski slopes, lifts, and winter terrain.
```javascript
MapStyle.WINTER
MapStyle.WINTER.DARK
```

### OCEAN
Maritime/nautical style with depth contours and marine features.
```javascript
MapStyle.OCEAN
```

---

## Data Visualization Styles

These styles are optimized for data overlays with minimal base map distractions.

### DATAVIZ
Clean background for data visualization.
```javascript
MapStyle.DATAVIZ
MapStyle.DATAVIZ.DARK    // Dark theme - ideal for glowing overlays
MapStyle.DATAVIZ.LIGHT   // Light theme
```

### BACKDROP
High contrast with hillshading, great for terrain-aware visualizations.
```javascript
MapStyle.BACKDROP
MapStyle.BACKDROP.DARK
```

---

## Accessing Style URLs (Advanced)

If you need the raw style URL (not recommended), you can access it via:

```javascript
const styleUrl = maptilersdk.MapStyle.STREETS.getUrl();
```

However, **always prefer using the enum directly** as it:
- Enables session billing optimization
- Automatically uses the latest style version
- Provides TypeScript type safety

---

## Style Change Best Practices

When changing styles at runtime, all custom layers are removed:

```javascript
function changeStyle(newStyle) {
  // Store current custom layer data if needed
  const myData = map.getSource('my-source')?._data;

  map.setStyle(newStyle);

  map.once('styledata', () => {
    if (myData) {
      addMyCustomLayer(myData);
    }
  });
}
```

---

## Language Configuration

Set map labels language globally:

```javascript
// Specific language
maptilersdk.config.primaryLanguage = maptilersdk.Language.CZECH;

// Secondary fallback language
maptilersdk.config.secondaryLanguage = maptilersdk.Language.ENGLISH;

// Auto-detection based on browser
maptilersdk.config.primaryLanguage = maptilersdk.Language.AUTO;

// Per-instance override
const map = new maptilersdk.Map({
  language: maptilersdk.Language.GERMAN
});

// Runtime change
map.setLanguage(maptilersdk.Language.FRENCH);
```

Available languages include:
- `Language.AUTO` (browser detection — default)
- `Language.LOCAL` (native language of each region)
- `Language.ENGLISH`
- `Language.GERMAN`
- `Language.FRENCH`
- `Language.SPANISH`
- `Language.ITALIAN`
- `Language.PORTUGUESE`
- `Language.CZECH`
- `Language.POLISH`
- `Language.DUTCH`
- `Language.CHINESE`
- `Language.JAPANESE`
- `Language.KOREAN`
- `Language.ARABIC`
- `Language.HINDI`
- `Language.RUSSIAN`
- `Language.TURKISH`
- And many more...
