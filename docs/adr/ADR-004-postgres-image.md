# ADR-004: PostgreSQL Image Selection

**Date:** 2026-10-01
**Status:** Accepted

## Context
The Vajra platform requires both PostGIS for spatial indexing and querying, and TimescaleDB for time-series hypertable management. We need an official, well-maintained Docker image that packages both extensions reliably, especially given our constraints of low memory environments in local development.

## Decision
We select `timescale/timescaledb-ha:pg15` as our PostgreSQL image.

## Alternatives Considered
- `postgis/postgis`: Does not include TimescaleDB out of the box.
- `timescale/timescaledb`: Standard image lacks PostGIS in some recent tags unless compiled manually.
- Building a custom image: Increases maintenance burden and build times.

## Consequences
- We get both extensions (PostGIS and TimescaleDB) readily available.
- We must ensure we constrain memory (`shared_buffers`) during local development as this image defaults to higher performance settings.
