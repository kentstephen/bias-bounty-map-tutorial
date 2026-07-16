# Bias Bounties @ Scale: Map Tutorials

Tutorial notebooks for the **Bias Bounties @ Scale** challenge (Humane Intelligence /
Reliabl) map-data extracts: Overture Maps and reference layers as cloud-native GeoParquet
on [Source Cooperative](https://source.coop/humane-intelligence/bias-bounty-mapping-equity-challenge).
Everything reads straight off the public bucket. No downloads, no credentials.

## The notebook

[`bias-bounty-explore-tutorial.ipynb`](bias-bounty-explore-tutorial.ipynb)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/kentstephen/bias-bounty-map-tutorials/blob/main/bias-bounty-explore-tutorial.ipynb)

1. Opening the files (DuckDB and GeoPandas, one line each)
2. Overture's nested columns (`names`, `categories`, `sources`)
3. An interactive map: draw a box, get the query code back

## Run it

All dependency versions are locked (pyproject.toml, uv.lock, and inline in the notebook).

```bash
uv sync && uv run jupyter lab          # local, from the lockfile
uvx juv run bias-bounty-explore-tutorial.ipynb   # or juv, from the notebook's inline metadata
```

Or open it in Colab with the badge above; the first cell installs the pinned dependencies.
