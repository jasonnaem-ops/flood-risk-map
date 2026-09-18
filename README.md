# Raritan Corridor Flood Risk Map

## Problem
Flood risk assessment often requires expensive, specialized software or
certified studies that aren't accessible for early-stage planning or public
awareness. This project explores whether a simple, transparent overlay model
using free public data can approximate relative flood risk across a
vulnerable river corridor — the New Brunswick–Piscataway reach of the
Raritan River, an area with a real flooding history (notably during
Hurricane Ida, 2021).

## Data Sources
- **Waterway geometry**: OpenStreetMap (via the `osmnx` Python library) —
  71 river/stream segments, including the Raritan River itself
- **Elevation**: Open-Elevation API, queried across a 1,600-point grid
  (40x40) spanning the study area (40.47°–40.52°N, -74.48°–-74.40°W)

## Method
For each of the 1,600 grid points:
1. **Elevation** was normalized (0–1) and inverted, so lower ground scores higher risk
2. **Distance to nearest waterway** was computed (in meters, via a projected
   coordinate system for accuracy) and similarly normalized and inverted
3. A **composite risk score** was calculated as a weighted blend:
   `risk = 0.6 x elevation_risk + 0.4 x proximity_risk`

This is a simplified, transparent overlay method, not a hydrological or
hydraulic model. It doesn't incorporate rainfall intensity, storm surge,
soil permeability, or drainage infrastructure, so it approximates relative
risk across the study area rather than predicting actual flood depths or
probabilities.

## Results
- Risk scores across the grid ranged from 0.19 (highest ground, farthest
  from water) to 0.99 (lowest ground, closest to water), with a mean of 0.59
- The highest-risk band closely traces the Raritan River's actual channel,
  visually validating that the two input factors (elevation + proximity)
  meaningfully capture flood-prone geography even without hydrological modeling

## Interactive Map
Built with Leaflet.js as a single self-contained HTML file, no server or
external map tiles required, so it works identically online or offline.
A lat/lon graticule is used in place of street-map tiles for a clean,
dependency-free presentation.

## Limitations & Future Work
- Not a certified flood study — omits rainfall, storm surge, soil
  permeability, and drainage infrastructure
- Uses a static elevation snapshot rather than a hydrologically-corrected
  DEM (digital elevation model), which could introduce noise in flat areas
- A logical next step would be validating this overlay against FEMA's
  National Flood Hazard Layer for the same corridor, or incorporating
  historical rainfall data to add a dynamic (storm-scenario) risk layer
- Grid resolution (40x40) is coarse; a finer grid or actual raster DEM
  processing (e.g., via `rasterio`) would improve spatial precision

## Tech Stack
Python (geopandas, osmnx, requests, shapely), Leaflet.js, HTML/CSS/JS

## How to Reproduce
1. Open the notebook in Google Colab
2. Run cells in order to pull waterway data (OSM), generate the grid,
   fetch elevation (Open-Elevation API), and compute risk scores
3. Export results as GeoJSON
4. Embed the GeoJSON into the provided HTML template to regenerate the map
