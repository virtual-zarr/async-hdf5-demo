---
title: async-hdf5 demo
---

Benchmarking [async-hdf5](https://github.com/maxrjones/async-hdf5) + [zarrs-python](https://github.com/ilan-gold/zarrs-python) for reading ICESat-2 ATL06 (land ice elevation) data directly from NASA's cloud archives.

## Motivation

[magg](https://github.com/englacial/magg) aggregates ICESat-2 point data into gridded products using 1,700+ parallel Lambda workers. Profiling revealed that HDF5 reading, not computation, is the bottleneck ([englacial/magg#5](https://github.com/englacial/magg/issues/5)). The pure-Python reader (h5coro) suffers from lock contention under concurrency and linear memory growth ([englacial/magg#4](https://github.com/englacial/magg/issues/4)).

async-hdf5 addresses this with:

- **Rust-based HDF5 parsing** -- no GIL, no lock contention
- **Async byte-range fetches** -- reads only the bytes needed, batched automatically
- **Zarr v3 store protocol** -- HDF5 files appear as Zarr stores to xarray

Combined with [zarrs-python](https://github.com/ilan-gold/zarrs-python), which replaces zarr-python's codec pipeline with a Rust implementation that parallelizes decompression across chunks, the entire data path from fetch to array runs in Rust.

## Prerequisites

- A [NASA Earthdata](https://urs.earthdata.nasa.gov/) account (free)
- Python 3.12+

## Notebooks

1. [**Explore ATL06**](./notebooks/01-explore.ipynb) -- Open an ATL06 granule over HTTPS, inspect the HDF5 structure, read elevation data
2. [**Performance comparison**](./notebooks/02-performance.ipynb) -- magg-like aggregation (all 6 beams, quality filter, weighted mean) over Antarctic ATL06 granules, comparing h5coro vs async-hdf5 + zarrs
3. [**VirtualiZarr integration**](./notebooks/03-virtual.ipynb) -- Create virtual Zarr references to HDF5 data without copying
4. [**Memory comparison**](./notebooks/04-memory.ipynb) -- Profile peak memory with memray
5. [**Performance comparison (S3)**](./notebooks/05-performance-s3.ipynb) -- Same aggregation via S3 direct access (requires us-west-2)

## Running locally

```bash
git clone https://github.com/virtual-zarr/async-hdf5-demo
cd async-hdf5-demo
uv sync
uv run jupyter lab
```
