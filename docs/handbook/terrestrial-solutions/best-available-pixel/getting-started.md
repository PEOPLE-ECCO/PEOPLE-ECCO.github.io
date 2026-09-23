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