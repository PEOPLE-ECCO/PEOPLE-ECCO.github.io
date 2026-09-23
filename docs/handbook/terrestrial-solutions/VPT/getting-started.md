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