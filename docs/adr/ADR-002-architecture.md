# ADR-002: Architecture Decisions

**Date**: 2026-10-01
**Status**: Accepted

## Context
Vajra is a real-time convective-scale nowcasting platform. It must ingest multi-modal meteorological data, fuse it, generate forecasts, and serve GIS clients in real-time, operating across three profiles: `lite` (laptop/single node), `full` (small cluster), and `national` (Kubernetes cluster). 

## Decision
Based on the `MASTER_RULES.md`, we establish the following architecture decisions:
1. **Backend**: Python 3.12, FastAPI, Pydantic v2, and stateless services.
2. **Event Bus**: Abstracted via `EventBus` interface. Uses Redis Streams for `lite`, and Redpanda/Kafka for `full`/`national`.
3. **Gridded Store**: Zarr on an `ObjectStore` interface (Local FS / MinIO / S3).
4. **Structured Store**: PostgreSQL + PostGIS + TimescaleDB for sources, tracking, and spatial data. (SQLite only for unit tests).
5. **Hot State**: Redis for latest frame caching and pub/sub.
6. **ML Framework**: PyTorch (with Lightning), LightGBM for cell-level hazard heads, ONNX Runtime for serving. Residual learning on advection baseline + Multimodal ConvLSTM with dynamic gating.
7. **Frontend**: Next.js + TypeScript + Tailwind + MapLibre GL + deck.gl for performant WebGL rendering.

## Consequences
- The system is inherently distributed and message-driven, enabling scale-out.
- Decoupling storage and buses behind interfaces allows local development (`lite`) without heavy infrastructure, while matching production patterns.
- Postgres+PostGIS replaces SQLite for multi-writer scalability.
