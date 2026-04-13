# Contributing

## Setup

```bash
git clone https://github.com/virtual-zarr/async-hdf5-demo
cd async-hdf5-demo
uv sync
uv run pre-commit install
```

## Editing notebooks

The `.py` percent-format scripts in `notebooks/` are the source of truth. The paired `.ipynb` files are kept in sync by a pre-commit hook.

To edit a notebook:

1. Edit the `.py` file directly, **or** open the `.ipynb` in Jupyter and edit there
2. On commit, the pre-commit hook runs `jupytext --sync` to update the other format

To convert manually:

```bash
uv run jupytext --sync notebooks/*.py
```

## Previewing the site

```bash
uv run --group dev myst start
```

## Building the site

```bash
uv run jupytext --sync notebooks/*.py
uv run --group dev myst build --html
```

Output goes to `_build/html/`.
