# Bias Bounties @ Scale: Map Tutorial

Tutorial notebook for the **Bias Bounties @ Scale** challenge (Humane Intelligence /
Reliabl, hosted on [Zindi](https://zindi.world/competitions/bias-bounty-mapping-equity-challenge)).
The challenge scores how well **Overture Maps** covers four US regions against authoritative
reference layers. The data ships as cloud-native GeoParquet on
[Source Cooperative](https://source.coop/humane-intelligence/bias-bounty-mapping-equity-challenge),
and everything here reads straight off that public bucket. No downloads, no credentials, no signup.

## The notebook

[`bias-bounty-explore-tutorial.ipynb`](bias-bounty-explore-tutorial.ipynb)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/kentstephen/bias-bounty-map-tutorial/blob/main/bias-bounty-explore-tutorial.ipynb)

1. **Opening the files.** DuckDB and GeoPandas, one line each, with the bucket settings that
   make `s3://` work and a `bbox` read that pulls a window out of a multi-GB layer without
   downloading it.
2. **Overture's nested columns.** `names` and `categories` are structs, `sources` is a list of
   structs. Dot access and `unnest` in DuckDB, `.str` and `explode` in GeoPandas, typed
   accessors with the pyarrow backend.
3. **An interactive map.** Pick a region, tick layers, and they read off the bucket for the
   current viewport (lonboard). All 13 `reference/` layers are there, plus the tract, tribal
   area and tribal tract outlines from `strata/`, two choropleths, and a place search. Draw a
   box on the map and the panel hands back a complete DuckDB + lonboard snippet that reads
   exactly those features.

The data paths are `reference/<region>/<region>-<source>-<layer>.parquet` for the four regions
`maricopa-az`, `northern-ca`, `eastern-ok` and `south-central-tx`. The Overture layers are
pinned to release `2026-08-19.0`. The
[README on the bucket](https://source.coop/humane-intelligence/bias-bounty-mapping-equity-challenge)
is the full reference for the layout, every layer, provenance, and the access gotchas; this
notebook is the hands-on companion to it.

## Run it

All dependency versions are locked (pyproject.toml, uv.lock, and inline in the notebook).

```bash
uv sync && uv run jupyter lab          # local, from the lockfile
uvx juv run bias-bounty-explore-tutorial.ipynb   # or juv, from the notebook's inline metadata
```

Or open it in Colab with the badge above; the first cell installs the pinned dependencies and
enables Colab's custom widget manager (restart the runtime if Colab asks).

Python >= 3.12. Viz is colorblind-safe throughout.

## Links

- Challenge: https://zindi.world/competitions/bias-bounty-mapping-equity-challenge
- Data and its README: https://source.coop/humane-intelligence/bias-bounty-mapping-equity-challenge
- Overture data docs: https://docs.overturemaps.org/
- lonboard: https://developmentseed.org/lonboard/
