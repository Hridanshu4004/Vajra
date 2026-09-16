# ADR-001: Toolchain and Python Versioning

**Date**: 2026-10-01
**Status**: Accepted

## Context
The project needs to run scientific Python libraries (Py-ART, pyiwr, satpy, cfgrib) alongside ML libraries (PyTorch, LightGBM) and backend services (FastAPI). The host machine runs Windows 11 with Python 3.14.5. Some libraries may not have pre-built wheels for Python 3.14 or may require C libraries (like ecCodes for cfgrib) that are difficult to compile natively on Windows.

## Decision
- Pin **Python 3.12** as the primary version for the ML and backend services to ensure maximum compatibility with the scientific stack.
- Use **uv** as the primary package manager for fast environment creation and dependency locking.
- Components that require C dependencies (like Py-ART, pyiwr, cfgrib, satpy) will run inside **Docker (Linux containers) or WSL2**.
- The frontend (apps/web) will use Node 22 (LTS) and **pnpm** workspaces.

## Consequences
- Developers on Windows must use Docker or WSL2 to run the full ingestion pipeline.
- We avoid fighting compilation errors for legacy geospatial libraries on Windows.
- `uv` provides exceptionally fast dependency resolution and environment management.
