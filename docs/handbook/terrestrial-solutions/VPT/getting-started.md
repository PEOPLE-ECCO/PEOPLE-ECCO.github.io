# Getting Started: Vegetation Productivity Trend (VPT)

The PEOPLE-ECCO VPT workflows are run locally with CWL and Docker. Two algorithms are available:

- Spectral Recovery (annual composite trajectory)
- Seasonal Sen's slope (monthly trajectory)

This page follows the same local CWL execution pattern used in the Integration and Interoperability section.

## Requirements

- Docker
- A CWL runner (`cwl-runner` / `cwltool`)
- CDSE credentials (OIDC client ID and secret)
- Local clone of `https://github.com/PEOPLE-ECCO/ecco-terrestrial-solutions`

## Step-by-step: Spectral Recovery VPT

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

1. Choose a Spectral Recovery parameter file

- Single site: `tooling/spectral-recovery/spectral_recovery_single_site_run_parameters.json`
- Basin loop: `tooling/spectral-recovery/spectral_recovery_basin_loop_run_parameters.json`

Recommended approach for runnable CWL runs: copy one template and edit it.

```bash
cp tooling/spectral-recovery/spectral_recovery_single_site_run_parameters.json my_spectral_recovery_params.json
```

The examples below are aligned to repository templates, but adapted for readability.

Example parameter file (single-site Spectral Recovery):

```json
{
	"execution_mode": "single_site",
	"reference_target_mode": "from_sites",
	"spatial_extent": {
		"type": "FeatureCollection",
		"features": [
			{
				"type": "Feature",
				"geometry": {
					"type": "Polygon",
					"coordinates": [[[105.34, 19.60], [105.34, 19.51], [105.39, 19.51], [105.39, 19.60], [105.34, 19.60]]]
				},
				"properties": {}
			}
		]
	},
	"spatial_extent_restoration_site": {
		"type": "FeatureCollection",
		"features": [
			{
				"type": "Feature",
				"geometry": {
					"type": "Polygon",
					"coordinates": [[[105.34, 19.57], [105.34, 19.55], [105.35, 19.55], [105.35, 19.57], [105.34, 19.57]]]
				},
				"properties": {}
			}
		]
	},
	"spatial_extent_reference_site": {
		"type": "FeatureCollection",
		"features": [
			{
				"type": "Feature",
				"geometry": {
					"type": "Polygon",
					"coordinates": [[[105.37, 19.54], [105.37, 19.53], [105.38, 19.53], [105.38, 19.54], [105.37, 19.54]]]
				},
				"properties": {}
			}
		]
	},
	"compositing_mode": "yearly",
	"years": [2018, 2019, 2020, 2021],
	"season_start": "01-01",
	"season_end": "04-30",
	"months": [1, 2, 3, 4],
	"indices_to_export": ["NBR"],
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
	"exclude_scl_classes": [1, 2, 3, 7, 8, 9, 10, 11],
	"export_profile": "spectral_recovery",
	"export_payload": "reflectance",
	"naming_convention": "profiled",
	"manifest_filename": "bap_manifest.json",
	"max_cloud_cover": 20,
	"spatial_resolution": 10,
	"dtc_max_distance": 30,
	"cloud_buffer_px": 2,
	"clip_to_aoi": true,
	"resume_existing_outputs": true,
	"sr_indices": ["NBR", "NDVI", "SAVI"],
	"sr_metrics": ["deltaIR", "R80P"],
	"sr_reference_start": "2018",
	"sr_reference_end": "2019",
	"sr_dist_rest_years": {
		"0": [2018, 2019]
	}
}
```

For basin-loop mode, set `execution_mode` to `basin_loop` and include:

- `aoi_basins_file`
- `basin_id_column`

1. Run single-site Spectral Recovery

```bash
cwl-runner cwl/spectral_recovery_single_site.cwl \
	--cdse_client_id="${OPENEO_AUTH_CLIENT_ID}" \
	--cdse_client_secret="${OPENEO_AUTH_CLIENT_SECRET}" \
	--parameters tooling/spectral-recovery/spectral_recovery_single_site_run_parameters.json \
	--run_name sr_run
```

1. Optional: run basin-loop Spectral Recovery

```bash
cwl-runner cwl/spectral_recovery_basin_loop.cwl \
	--cdse_client_id="${OPENEO_AUTH_CLIENT_ID}" \
	--cdse_client_secret="${OPENEO_AUTH_CLIENT_SECRET}" \
	--parameters tooling/spectral-recovery/spectral_recovery_basin_loop_run_parameters.json \
	--aoi_basins_file tooling/spectral-recovery/vietnam_ma_test_basins.geojson \
	--run_name sr_basin_run
```

## Step-by-step: Seasonal Sen VPT

You can run Seasonal Sen in two ways.

### Option A. Run BAP and Seasonal Sen in one CWL workflow

```bash
cwl-runner cwl/bap_seasonal_sen.cwl \
	--cdse_client_id="${OPENEO_AUTH_CLIENT_ID}" \
	--cdse_client_secret="${OPENEO_AUTH_CLIENT_SECRET}" \
	--bap_parameters tooling/seasonal-sen/bap_for_seasonal_sen_run_parameters.json \
	--seasonal_sen_parameters tooling/seasonal-sen/seasonal_sen_run_parameters_bulgaria_map_example_slope_test.json \
	--bap_run_name bulgaria_bap \
	--seasonal_sen_run_name bulgaria_sen
```

When using the combined workflow, ensure the seasonal parameter file expects BAP inputs from `/bap_composites`.

Example Seasonal Sen parameter file:

```json
{
	"spatial_extent_restoration_site": {
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
	"bap_composite_dir": "/bap_composites",
	"bap_manifest_file": "/bap_composites/bap_manifest.json",
	"reference_sites_file": null,
	"reference_bap_composite_dir": null,
	"reference_bap_manifest_file": null,
	"expected_bap_profile": "seasonal_sen",
	"index_name": "SAVI",
	"output_metrics": ["slope_intercept", "percent_change"],
	"restoration_id_column": null,
	"restoration_year_column": null,
	"reference_baseline_year": 2021,
	"target_fraction": 0.8,
	"reference_years_before_restoration": 3,
	"start_year_fallback": 2020,
	"end_year": 2021,
	"period": 3,
	"block_pixels": 20000,
	"all_touched": false
}
```

### Option B. Run Seasonal Sen from an existing BAP output

```bash
cwl-runner cwl/seasonal_sen.cwl \
	--cdse_client_id="${OPENEO_AUTH_CLIENT_ID}" \
	--cdse_client_secret="${OPENEO_AUTH_CLIENT_SECRET}" \
	--parameters tooling/seasonal-sen/seasonal_sen_run_parameters_bulgaria_map_example_slope_test.json \
	--bap_sen /path/to/bap_run/output \
	--run_name sen_run
```

## Inspect outputs

For both VPT modes, inspect the run output directory and verify expected metrics, especially:

- `R80P`
- `DeltaIR`

## Related pages

- [Theoretical Background](theoretical-background.md)
- [Key Considerations](key-considerations.md)