# Getting Started: Submerged Aquatic Vegetation (SAV)

The PEOPLE-ECCO aquatic SAV and Connectivity workflow uses openEO to fetch and subset Sentinel-2 imagery from CDSE, then applies a machine-learning prediction model to post-process and classify submerged aquatic vegetation. The code is available on its ![github repository](https://github.com/PEOPLE-ECCO/ecco-aquatic-solutions/tree/main).

## Requirements

- Docker
- A CWL runner (for example `cwl-runner` via `cwlref-runner`)
- CDSE credentials (OIDC client ID and secret)
- The `ecco-aquatic-solutions` repository

Example install of a CWL runner in a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
pip install cwlref-runner
```

## Step-by-step: running SAV locally

### 1. Clone and enter the repository

```bash
git clone https://github.com/PEOPLE-ECCO/ecco-aquatic-solutions.git
cd ecco-aquatic-solutions
```
### 2. Choose a SAV parameter file

A template is provided at `tooling/sav/sav_run_parameters_malaysia.json`. Copy it and edit it for your area of interest:

```json
{
  "rangestart": "2023-01-01",
  "rangeend": "2023-01-23",
  "spatial_extent": {
    "west": 118.54074,
    "south": 4.31173,
    "east": 118.58351,
    "north": 4.34025,
    "crs": "EPSG:4326"
  },
  "maxcloudcover": 40,
  "swir_threshold": 350,
  "sav_prob_threshold": 5000,
  "area_name": "Malaysia"
}
```

| Parameter | Description |
|---|---|
| `rangestart`, `rangeend` | Time window for imagery (YYYY-MM-DD) |
| `spatial_extent` | Bounding box of the area of interest and its CRS |
| `maxcloudcover` | Maximum scene cloud cover (%) |
| `swir_threshold` | SWIR reflectance threshold used to separate land from water and remove sun glint |
| `sav_prob_threshold` | Model probability cut-off for classifying a pixel as SAV (scaled 0–10000, so 5000 = 50%) |
| `area_name` | Label for the area, used to select the model. Two possibilities exist for this value, either `Malaysia` to use the model trained on tropical waters in Malaysia, or `PEI` to use the model trained in Canada for sub-polar regions. |

### 3. Run with CWL

```bash
# Build Docker Image
docker build -f tooling/sav/sav.Dockerfile \
    -t ghcr.io/people-ecco/dhi-sav:latest .

# set variables
export OPENEO_AUTH_CLIENT_ID="..."
export OPENEO_AUTH_CLIENT_SECRET="..."
export OUTPUT_PATH="./target/cwl_output"

# Run using cwl-runner
export $(cat .env | xargs) && cwl-runner cwl/sav.cwl --cdse_client_id=${OPENEO_AUTH_CLIENT_ID} --cdse_client_secret=${OPENEO_AUTH_CLIENT_SECRET} --parameters tooling/sav/sav_run_parameters_malaysia.json --run_name sav_run
```

You can also put these in a `.env` file (see `example.env`) and load them with `export $(cat .env | xargs)`.

### 4. Run SAV with CWL

```bash
cwl-runner cwl/sav.cwl \
  --cdse_client_id="${OPENEO_AUTH_CLIENT_ID}" \
  --cdse_client_secret="${OPENEO_AUTH_CLIENT_SECRET}" \
  --parameters tooling/sav/sav_run_parameters_malaysia.json \
  --run_name sav_run
```

### 5. Inspect outputs

Results are written to the CWL output directory (by default the current directory; set `OUTPUT_PATH`, e.g. `./target/cwl_output`, to change it), in a folder named after `--run_name`.

## Running without CWL

```bash
export OPENEO_AUTH_CLIENT_ID="..."
export OPENEO_AUTH_CLIENT_SECRET="..."

python tooling/sav/run_sav.py \
    --parameters tooling/sav/sav_run_parameters_malaysia.json \
    --output-dir ./target/output \
    --run-name sav_run_01
```

## Related pages

- [ecco-aquatic-solutions README](https://github.com/PEOPLE-ECCO/ecco-aquatic-solutions/blob/main/README.md)

## Suggested first run

- Start with a single AOI, the relevant model and limited time period.

- Preview available Sentinel 2 imagery on the [Copernicus Browser](!https://browser.dataspace.copernicus.eu/), and select a time period with clear water and atmospheric conditions. It is also possible on this viewer to change the cloud cover percentage to preview which scenes would be available at different values.

- If there is a single clear image, you can select a very short time window including only this scene. This type of scene generally yields good prediction results, and is fast to run.

- Inspect outputs for misclassification in turbid or deep water, both for the entire time series, and on per-scene basis.

- Adjust the model parameters if needed.
