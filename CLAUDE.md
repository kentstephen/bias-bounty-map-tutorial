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
- Layout: `reference/<region>/<region>-<source>-<layer>.parquet` + `boundaries/all-aois.geojson`
  - The bucket previously served these under `geoparquet/`; `reference/` is the same 13 layers
    and is what the notebook reads now. `geoparquet/` still exists but is being dropped, do not
    use it.
  - `strata/<region>/` ships 24 more parquet files per region, but only **five have a geometry
    column**: `census-tracts`, `census-aiannh`, `census-tribal-tracts`,
    `census-tribal-subdivisions` (maricopa-az and eastern-ok only), and `noaa-ghcn-stations`.
    The other 19 are `*-tract-table` files: no geometry, one row per tract, GEOID-keyed, 8 to
    226 columns, each with a `.csv` twin. `strata-tract-table` (226 cols) is a curated
    cross-source summary, not a superset: the union of the 18 source tables is 868 columns.
  - The notebook takes the four boundary polygons and nothing else. Decisions (Stephen's, 2026-07-28):
    no `strata/national/` (it is `national-census-tracts.parquet` at 224 MB and breaks the AOI
    framing), no tract tables (no geometry, so nothing to map), no `noaa-ghcn-stations` (a point
    layer, not a boundary). If the tract tables are ever wanted they join to `census-tracts` on
    GEOID and want a table+column picker, not a layer toggle.
  - The old TODO here named `cdc-svi`, `nchs-urban-rural`, `usda-ruca`, `usda-rucc`. Those are
    really `svi-tract-table`, `nchs-tract-table`, `ruca-tract-table`, `rucc-tract-table`, all
    non-geometry, all out of scope per the above.
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
- Viz is colorblind-safe only (no red-vs-green encodings). Saturation in `LAYERS` carries the
  read strategy: sparse whole-file layers are saturated Okabe-Ito, the five viewport reads are
  pale (they blanket the screen). The two choropleths use different ramps (cividis, viridis) so
  both can be on at once and still be told apart.
- **Map panel rules that are load-bearing, do not undo them:**
  - `MAX_LAYERS` caps only the layers flagged `big` in `LAYERS` (the five viewport reads that
    box up below `MINZOOM`). Everything read whole, boundaries included, is unlimited.
  - `rebuild()` must `close()` the Map it replaces. Each lonboard `Map` is an anywidget model
    with its own WASM instance; leaking them kills the map with "Cannot allocate Wasm memory
    for new instance".
  - Only build a new `Map` when one is genuinely needed (newly ticked layer, region change,
    reload button). Untick, reorder and boundary toggles `restack()` the live map.
  - `layer_stack()` runs on every camera move, so it hands back cached layer instances and never
    rebuilds them. Reusing instances is also the lonboard #1044 fill-drop workaround.
  - No threads and no timers anywhere. Colab only reliably delivers widget updates from
    browser-event comm handlers; a time-based debounce would reintroduce the old Colab bug.
  - A layer that was showing row-group boxes must never be reused as if it held features, or it
    stays boxed forever past `MINZOOM`. Reuse requires the layer to have been in the previous
    *read* set.
  - `on_region` holds `state["busy"]` and disables the search while a region loads, clearing both
    in a `finally`. Two camera destinations at once means two `Map` rebuilds, and that burst is
    what turns the notebook output grey. Both halves matter: the flag catches an event already in
    flight, the disabled widget is the affordance.
- This repo is public: no contract, billing, or coordination details in committed files.
