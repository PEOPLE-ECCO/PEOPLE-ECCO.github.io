# Getting Started: Habitat Metrics

Habitat metrics turn the classified SAV (and coral) rasters into vector habitat patches and describe how they are connected. For each patch it computes:

- `patch_id`: unique patch identifier
- `area_ha`: patch area in hectares
- `neighbors`: IDs of other patches within the neighbour distance
- `neigh_area_ha`: total area of those neighbouring patches

> **Note:** Habitat metrics already run as the last step of the main SAV workflow (`Algorithm.run` in `src/main.py`), after prediction. If you followed [Getting Started: SAV](getting-started.md), the habitat metric GeoJSON files have already been created. You only need this page to re-run the step on existing predictions, for example with different smoothing or neighbour settings.

## Requirements

- Python 3 with the repository dependencies installed
- The `ecco-aquatic-solutions` repository
- Aggregated prediction rasters from an earlier SAV run

No CDSE credentials are needed, because this step only reads local files.

```bash
git clone https://github.com/PEOPLE-ECCO/ecco-aquatic-solutions.git
cd ecco-aquatic-solutions
python3 -m venv venv
source venv/bin/activate
pip install geopandas==1.1.1 rasterio==1.5.0 scikit-image==0.25.2 numpy==2.3.4 pandas==2.3.3 pystac
```

> **Note:** Install only the packages this step needs, as shown above. The full `requirements.txt` pins `gdal`, which pip can only build when the GDAL system libraries are already installed.

## Input layout

`habitat_metric` takes a prediction directory and looks in its `aggr_results/` subfolder for single-band GeoTIFFs where pixel value `1` marks habitat:

```
<prediction_dir>/
└── aggr_results/
    ├── <...>aggregated_sav.tif
    └── <...>aggregated_coral.tif
```

The main workflow writes these rasters to `predictions/` inside its working directory.

## Running habitat metrics on its own

`src/habitat_metrics.py` has no command-line interface of its own, but you can call it from the repository root with a short Python one-liner:

```bash
python -c "
from pathlib import Path
from pystac import Catalog
from src.habitat_metrics import habitat_metric

catalog = Catalog(id='habitat-metrics', description='Habitat metrics run')
habitat_metric(Path('path/to/predictions'), catalog)
"
```

Replace `path/to/predictions` with your prediction directory, which is the folder that contains `aggr_results/`.

> **Check the console output.** The function prints `Calculating metrics for: ...` for each raster it finds. If nothing is printed, no matching rasters were found, so check the path. Errors are printed as `Error while calculating habitat_metrics: ...` rather than raised, so the command can finish without an error even when a file failed.

### Optional parameters

| Parameter | Default | Description |
|---|---|---|
| `buffer_distance` | `10` | Distance (metres) used to smooth patch outlines with a negative then positive buffer |
| `neighbor_threshold` | `1000` | Distance (metres) within which other patches count as neighbours |

For example, to count patches within 500 m as neighbours:

```python
habitat_metric(
    Path("path/to/predictions"), catalog, buffer_distance=10, neighbor_threshold=500
)
```

## Process

1. **Read** each aggregated raster and build a binary mask of pixels equal to `1`.
2. **Remove** objects smaller than 1000 pixels and apply a morphological opening.
3. **Vectorise** the mask into polygons, then simplify and smooth them.
4. **Reproject** to the local UTM zone and compute `area_ha`.
5. **Buffer** each patch by `neighbor_threshold` to find neighbours and `neigh_area_ha`.

If a raster has no habitat pixels, an empty result is produced.

## Outputs

For each input raster, a GeoJSON file is written next to it in `aggr_results/`, named after the raster with a `.geojson` suffix (for example, `..._aggregated_sav.tif.geojson`). Each file is also registered as a STAC item (type `geojson-sav` or `geojson-coral`) in the catalog you passed in.
