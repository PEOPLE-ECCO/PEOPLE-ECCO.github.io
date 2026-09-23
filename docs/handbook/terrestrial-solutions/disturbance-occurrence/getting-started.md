# Getting Started: Vegetation Disturbance Occurrence

In the terrestrial solutions package, Vegetation Disturbance Occurrence is implemented as the Breaks workflow (`cwl/breaks.cwl`). It consumes yearly BAP composites and returns disturbance change layers and metrics.

This page describes how to run it locally with CWL.

## Requirements

- Docker
- A CWL runner (`cwl-runner` / `cwltool`)
- CDSE credentials (OIDC client ID and secret)
- Local clone of `https://github.com/PEOPLE-ECCO/ecco-terrestrial-solutions`
- A BAP output directory (from `cwl/bap.cwl`) containing yearly composites and `bap_manifest.json`

## Step-by-step: running Breaks locally

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

1. Generate yearly BAP composites (if not already available)

```bash
cwl-runner cwl/bap.cwl \
	--cdse_client_id="${OPENEO_AUTH_CLIENT_ID}" \
	--cdse_client_secret="${OPENEO_AUTH_CLIENT_SECRET}" \
	--parameters tooling/bap/bap_run_parameters_breaks.json \
	--run_name bap_breaks_run
```

1. Select Breaks parameters

Start from `tooling/breaks/breaks_run_parameters.json`.

Key fields in this file are:

- `index_name` (for example SAVI or NBR)
- `break_threshold`
- `index_scale`
- `output_name`

1. Run Breaks with CWL

```bash
cwl-runner cwl/breaks.cwl \
	--cdse_client_id="${OPENEO_AUTH_CLIENT_ID}" \
	--cdse_client_secret="${OPENEO_AUTH_CLIENT_SECRET}" \
	--parameters tooling/breaks/breaks_run_parameters.json \
	--baps bap_breaks_run/output \
	--run_name breaks_run
```

## Inspect outputs

The run writes outputs under the `breaks_run` directory. Review the break layers and change metrics before thresholding or downstream use.

## Typical downstream use

- Convert Breaks outputs to categorical disturbance occurrence with local thresholds.
- Use the resulting disturbance layer as input to Habitat Disturbance Rating.

## Related pages

- [Theoretical Background](theoretical-background.md)
- [Key Considerations](key-considerations.md)