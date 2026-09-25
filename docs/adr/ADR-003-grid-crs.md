# ADR-003: Grid Projections and Tiling

**Date**: 2026-10-01
**Status**: Accepted

## Context
Vajra needs to calculate physical dimensions of severe weather phenomena (cell area, advection speed, convergence) across the entirety of India. We must choose a Coordinate Reference System (CRS) for the internal gridded data and a tiling strategy for distributed computation. EPSG:4326 (WGS84) is a geographic CRS; computing areas directly on it leads to severe distortion (cells in Kashmir would be physically smaller in area than cells in Tamil Nadu).

## Decision
- **CRS**: Internal grids will use an equal-area or conformal metric projection suitable for India, specifically **EPSG:7755 (India Lambert Conformal Conic)**, to ensure physical measurements like cell tracking and radar mosaic grids remain geographically accurate in metres.
- **Data Exchange**: All public APIs and GeoJSON outputs will be projected back to **EPSG:4326** to ensure compatibility with MapLibre GL and standard web maps.
- **Tiling**: We will use a fixed 256x256 tile system on the metric grid with configurable overlaps. Global tile IDs will map directly to array chunks in Zarr, enabling parallel processing without edge artifacts.
- **Location Indexing**: We will use Uber's **H3** at resolutions 7 (approx 1.2km) and 8 (approx 460m) for rapid spatial joins and point-in-polygon checks without heavy PostGIS operations.

## Consequences
- Requires reprojection for ingestion (e.g., radar sweeps to EPSG:7755) and serving (EPSG:7755 to EPSG:4326).
- H3 simplifies point/polygon indexing.
- 256x256 tiles map perfectly to GPU threads and CNN architectures for the ML models.
