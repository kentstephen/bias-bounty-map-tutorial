# Bias Bounties @ Scale: Map Tutorials (project notes)

Public tutorial repo for the **Bias Bounties @ Scale** challenge (Humane Intelligence /
Reliabl, hosted on Zindi). This is the participant-facing culmination of the data-prep work
done in the private repo `~/dev/projects/bounty-bias-cng-eda-etl/` (notebooks 19-22 there
are the tutorial lineage; this repo publishes the final one). Session/contract state lives
in `.claude/memory/MEMORY.md` (gitignored); this file is public.

## What's here
- `bias-bounty-explore-tutorial.ipynb`: the tutorial. Opening the extracts (DuckDB +
  GeoPandas), Overture's nested columns, and an interactive lonboard map (draw a box, get
  the query code back). Everything reads from the live public bucket; no downloads, no creds.
- More extract notebooks from the private repo may be added later (19-21: overture-tutorial,
  coverage-explorer, coverage-explorer-simple).

## Data
- Product: `source.coop/humane-intelligence/bias-bounty-mapping-equity-challenge`
- Bucket: `s3://us-west-2.opendata.source.coop/humane-intelligence/bias-bounty-mapping-equity-challenge/`
- Layout: `geoparquet/<region>/<region>-<source>-<layer>.parquet` + `boundaries/all-aois.geojson`
- Regions: `maricopa-az`, `northern-ca`, `eastern-ok`, `south-central-tx`
- Overture release pinned `2026-06-17.0`. The data-side README in the private repo is the
  authoritative access reference (s3:// vs https, anonymous S3, DuckDB settings).

## Conventions (do not drift)
- **Dependency versions are locked in three places that must agree:** `pyproject.toml`
  (== pins, uv.lock), the PEP 723 block in the notebook's first code cell (for
  `uvx juv run`), and the `uv pip install --system` line in that same cell (for Colab).
  Any dep change updates all three plus `uv.lock`.
- Runs three ways: `uv sync` + jupyter lab (dev group has jupyterlab), `uvx juv run
  <notebook>`, or Colab via the badge (URL assumes GitHub `kentstephen/bias-bounty-map-tutorial`,
  branch `main`).
- Python >= 3.12. Notebook must stay runnable top-to-bottom on a fresh kernel with no
  credentials configured (and also WITH AWS creds configured: anonymous S3 everywhere).
- Viz is colorblind-safe only (no red-vs-green encodings).
- This repo is public: no contract, billing, or coordination details in committed files.
