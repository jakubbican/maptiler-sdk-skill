# MapTiler SDK — CDN Reference

## CDN URL Varianty

MapTiler CDN podporuje dva přístupy pro načítání SDK:

### `latest` — vždy aktuální verze (doporučeno pro příklady a prototypy)

```html
<link href="https://cdn.maptiler.com/maptiler-sdk-js/latest/maptiler-sdk.css" rel="stylesheet" />
<script src="https://cdn.maptiler.com/maptiler-sdk-js/latest/maptiler-sdk.umd.min.js"></script>
```

✅ Vždy načte nejnovější vydanou verzi — příklady zůstanou funkční bez manuálního updatu.
⚠️ Při vydání nové major verze (v4.x) může přinést breaking changes. Pro produkci doporučit pinned verzi přes npm.

### Pinned verze — pro produkci

```html
<link href="https://cdn.maptiler.com/maptiler-sdk-js/v3.11.1/maptiler-sdk.css" rel="stylesheet" />
<script src="https://cdn.maptiler.com/maptiler-sdk-js/v3.11.1/maptiler-sdk.umd.min.js"></script>
```

✅ Žádné překvapivé breaking changes.
⚠️ Vyžaduje ruční update URL při nových verzích.
→ **Pro produkci vždy preferovat npm** (viz níže) — umožňuje lock přes `package.json`.

### URL vzor pro konkrétní verzi

```
https://cdn.maptiler.com/maptiler-sdk-js/v{VERSION}/maptiler-sdk.umd.min.js
https://cdn.maptiler.com/maptiler-sdk-js/v{VERSION}/maptiler-sdk.css
```

---

## Globální proměnná (CDN / UMD)

SDK po načtení přes CDN exponuje globální objekt:
```javascript
window.maptilersdk
```

---

## Version History

| Version | Release Date | Notes |
|---------|--------------|-------|
| 3.11.1 | Feb 2025 | Latest stable — MapTilerAnimation, AnimatedRouteLayer |
| 3.11.0 | Feb 2025 | `setProjection()` s persistencí, deprecated `enableGlobeProjection()` |
| 3.10.2 | Jan 2025 | RTL text re-enabled |
| 3.9.0 | Jan 2025 | ImageViewer expanded |
| 3.8.0 | Dec 2024 | ImageViewer, custom controls, halo/space animation toggles |
| 3.5.0 | Nov 2024 | Globe `halo` a `space` constructor options |
| 3.4.1 | Oct 2024 | MapLibre GL JS 5.6.0 |
| 3.2.0 | Sep 2024 | MapLibre GL JS 5.3.1 |
| **3.0.0** | **Aug 2024** | **MAJOR** — MapLibre GL JS 5.0.0, globe projection, MaptilerProjectionControl |
| 2.4.0 | Legacy | Poslední v2 release — nepoužívat pro nové projekty |

### v3.0.0 Breaking Changes

- Upgrade na **MapLibre GL JS 5.0.0** — viz [MapLibre v5 migration guide](https://maplibre.org/maplibre-gl-js/docs/guides/migration-v4-to-v5/)
- Všechny doplňkové moduly (`@maptiler/weather`, `@maptiler/3d`, ...) musí být aktualizovány na v3-kompatibilní verze
- Internals NavigationControl změněny kvůli podpoře globe projekce

---

## Kompletní HTML šablona (CDN)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapTiler SDK Map</title>

  <!-- latest = vždy aktuální verze -->
  <link href="https://cdn.maptiler.com/maptiler-sdk-js/latest/maptiler-sdk.css" rel="stylesheet" />

  <style>
    body { margin: 0; padding: 0; }
    #map { position: absolute; top: 0; bottom: 0; left: 0; right: 0; }
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
      center: [0, 0],
      zoom: 2
    });
  </script>
</body>
</html>
```

---

## Geocoding Control CDN

```html
<script src="https://cdn.maptiler.com/maptiler-geocoding-control/latest/maptilersdk.umd.js"></script>
<link href="https://cdn.maptiler.com/maptiler-geocoding-control/latest/style.css" rel="stylesheet" />
```

---

## NPM (doporučeno pro produkci)

```bash
npm install @maptiler/sdk
```

```javascript
import * as maptilersdk from '@maptiler/sdk';
import '@maptiler/sdk/dist/maptiler-sdk.css';
```

Výhody oproti CDN:
- Tree shaking — menší bundle
- TypeScript typy
- Lepší integrace s build tools
- Version lock přes `package.json` + `package-lock.json`
