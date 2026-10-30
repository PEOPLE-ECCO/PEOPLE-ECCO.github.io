# Getting Started: Habitat Time Series

The habitat time series tool compares yearly habitat extent results over the same area. It computes per-pixel statistics showing how often habitat was detected, when it was last seen, and where it appeared or disappeared between the first and last year.

> **Important:** The tool is meant to compare **different years over the exact same area**. To be comparable, every yearly result must also cover **the same season** (for example July to September each year), so that seasonal changes in vegetation, water clarity or image availability are not mistaken for real habitat change. If the yearly maps were not on the same grid, the aligned copies used for the calculation are saved in `<output-dir>/aligned/<habitat>/`.

## Requirements

- Python 3 with `numpy`, `pandas`, `rasterio`, `rioxarray` and `xarray`
- The [`ecco-aquatic-solutions`](https://github.com/PEOPLE-ECCO/ecco-aquatic-solutions) repository
- At least two yearly results from the [Habitat Extent](../getting-started.md) workflow

```bash
git clone https://github.com/PEOPLE-ECCO/ecco-aquatic-solutions.git
cd ecco-aquatic-solutions
python3 -m venv venv
source venv/bin/activate
pip install numpy pandas rasterio rioxarray xarray
```

No CDSE credentials are needed, because the tool only reads local results.

## Step-by-step

### 1. Run the Habitat Extent workflow for each year

Run the main SAV workflow once per year, changing **only the years** in the time window. Keep the spatial extent, season, model and thresholds identical for every run. For example, for 2023 and 2024:

```json
{
  "rangestart": "2023-07-01",
  "rangeend": "2023-09-30",
  "spatial_extent": {"west": 118.54074, "south": 4.31173, "east": 118.58351, "north": 4.34025, "crs": "EPSG:4326"},
  "maxcloudcover": 40,
  "swir_threshold": 350,
  "sav_prob_threshold": 5000,
  "area_name": "Malaysia"
}
```

```json
{
  "rangestart": "2024-07-01",
  "rangeend": "2024-09-30",
  "spatial_extent": {"west": 118.54074, "south": 4.31173, "east": 118.58351, "north": 4.34025, "crs": "EPSG:4326"},
  "maxcloudcover": 40,
  "swir_threshold": 350,
  "sav_prob_threshold": 5000,
  "area_name": "Malaysia"
}
```

### 2. Organise the results

Keep one result folder per year. The tool searches each folder (including subfolders) for the binary habitat maps `<date>_aggregated_sav.tif` and `<date>_aggregated_coral.tif`, and reads the year from the start of the file name.

```
results/
├── 2023/predictions/aggr_results/2023-08-14_aggregated_sav.tif
├── 2024/predictions/aggr_results/2024-08-09_aggregated_sav.tif
└── 2025/predictions/aggr_results/2025-08-21_aggregated_sav.tif
```

Each folder must contain only one result per year.

### 3. Run the time series

From the repository root:

```bash
python -m src.time_series \
  --input-folders results/2023 results/2024 results/2025 \
  --output-dir results/time_series
```

| Argument | Default | Description |
|---|---|---|
| `--input-folders` | required | Yearly result folders, at least two |
| `--output-dir` | required | Folder where the time series results are written |
| `--habitat-types` | `sav coral` | Habitat types to process |
| `--extent` | `intersection` | Common extent used if the inputs are not on the same grid: `intersection` or `union` |
