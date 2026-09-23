# Getting Started: Best-Available Pixel (BAP)

Every PEOPLE-ECCO terrestrial workflow starts from Best-Available Pixel (BAP) composites. This page describes how to run BAP locally with CWL, following the same EOAP-style approach used in the Integration and Interoperability section.

## Requirements

- Docker
- A CWL runner (for example `cwl-runner` / `cwltool`)
- CDSE credentials (OIDC client ID and client secret)
- The terrestrial solutions package from GitHub: `https://github.com/PEOPLE-ECCO/ecco-terrestrial-solutions`

Install a CWL runner (example):

```bash
python3 -m venv venv
source venv/bin/activate
pip install cwlref-runner
```

## Step-by-step: running BAP locally

1. Clone and enter the repository

```bash
git clone https://github.com/PEOPLE-ECCO/ecco-terrestrial-solutions.git
cd ecco-terrestrial-solutions
```

1. Set CDSE credentials

```bash
export OPENEO_AUTH_CLIENT_ID="<your CDSE client id>"
export OPENEO_AUTH_CLIENT_SECRET="<your CDSE client secret>"
```

1. Choose a BAP parameter file

Common starters in the repository:

- `tooling/bap/bap_run_parameters_breaks.json` for yearly composites used by Breaks/Disturbance Occurrence.
- `tooling/seasonal-sen/bap_for_seasonal_sen_run_parameters.json` for monthly composites used by Seasonal Sen.

Recommended approach for runnable CWL runs: copy one of the repository templates and edit it.

```bash
cp tooling/bap/bap_run_parameters_breaks.json my_bap_params.json
```

The examples below are aligned to repository templates, but they are adapted for readability.

Example parameter file (yearly BAP for Breaks):

```json
{
  "spatial_extent": {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "geometry": {
          "type": "Polygon",
          "coordinates": [[[29.60, 4.09], [29.56, 4.09], [29.56, 4.07], [29.60, 4.07], [29.60, 4.09]]]
        },
        "properties": {}
      }
    ]
  },
  "spatial_extent_file": null,
  "compositing_mode": "yearly",
  "years": [2020, 2021, 2022, 2023],
  "season_start": "09-01",
  "season_end": "11-30",
  "months": [9, 10, 11],
  "indices_to_export": ["SAVI"],
  "savi_l": 0.5,
  "tcw_coefficients": {
    "B02": 0.1509,
    "B03": 0.1973,
    "B04": 0.3279,
    "B08": 0.3406,
    "B11": -0.7112,
    "B12": -0.4572
  },
  "include_reflectance_bands": false,
  "exclude_scl_classes": [1, 2, 3, 7, 8, 9, 10],
  "export_profile": "breaks",
  "export_payload": "indices",
  "naming_convention": "profiled",
  "manifest_filename": "bap_manifest.json",
  "max_cloud_cover": 20,
  "spatial_resolution": 10,
  "dtc_max_distance": 30,
  "cloud_buffer_px": 2,
  "score_weight_dtc": 1.0,
  "score_weight_date": 0.8,
  "score_weight_coverage": 0.5,
  "clip_to_aoi": true,
  "resume_existing_outputs": true
}
```

Example parameter file (monthly BAP for Seasonal Sen):

```json
{
  "spatial_extent": {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "geometry": {
          "type": "Polygon",
          "coordinates": [[[105.33, 19.89], [105.33, 19.87], [105.34, 19.87], [105.34, 19.89], [105.33, 19.89]]]
        },
        "properties": {}
      }
    ]
  },
  "spatial_extent_file": null,
  "compositing_mode": "monthly",
  "years": [2020, 2021],
  "season_start": "05-01",
  "season_end": "07-31",
  "months": [5, 6, 7],
  "indices_to_export": ["SAVI"],
  "savi_l": 0.5,
  "tcw_coefficients": {
    "B02": 0.1509,
    "B03": 0.1973,
    "B04": 0.3279,
    "B08": 0.3406,
    "B11": -0.7112,
    "B12": -0.4572
  },
  "include_reflectance_bands": false,
  "exclude_scl_classes": [1, 2, 3, 7, 8, 9, 10],
  "export_profile": "seasonal_sen",
  "export_payload": "indices",
  "naming_convention": "profiled",
  "manifest_filename": "bap_manifest.json",
  "max_cloud_cover": 30,
  "spatial_resolution": 10,
  "dtc_max_distance": 30,
  "cloud_buffer_px": 2,
  "score_weight_dtc": 1.0,
  "score_weight_date": 0.8,
  "score_weight_coverage": 0,
  "clip_to_aoi": true,
  "resume_existing_outputs": true
}
```

1. Run BAP with CWL

```bash
cwl-runner cwl/bap.cwl \
  --cdse_client_id="${OPENEO_AUTH_CLIENT_ID}" \
  --cdse_client_secret="${OPENEO_AUTH_CLIENT_SECRET}" \
  --parameters tooling/bap/bap_run_parameters_breaks.json \
  --run_name bap_breaks_run
```

1. Inspect outputs

The run writes a directory named after `--run_name`, with:

- `output/bap_manifest.json`
- BAP composite GeoTIFF files for the requested years/months

## Using BAP outputs downstream

- For Vegetation Disturbance Occurrence (Breaks), pass `<run_name>/output` as the `baps` input in `cwl/breaks.cwl`.
- For Seasonal Sen, use either:
  - the combined workflow `cwl/bap_seasonal_sen.cwl`, or
  - `cwl/seasonal_sen.cwl` with `--bap_sen <run_name>/output`.

## Related pages

- [Theoretical Background](theoretical-background.md)
- [Key Considerations](key-considerations.md)