# Solutions Integration and Interoperability

Every PEOPLE-ECCO Solution is packaged as an Earth Observation Application Package (EOAP): a CWL (Common Workflow Language) workflow that runs the algorithm inside a Docker container. Because the packaging is standardized, any Solution can be executed locally with a CWL runner – outside of the PEOPLE-ECCO Platform – as long as the requirements below are met.

This page walks through running a Solution locally, using **SAV** (Submerged Aquatic Vegetation, from `ecco-marine-solutions`) as a worked example.

## Requirements

- **Docker**: to pull and run the Solution's published container image.
- **A CWL runner**: e.g. `cwltool`/`cwl-runner`, installed via:
  ```bash
  python3 -m venv venv
  source venv/bin/activate
  pip install cwlref-runner
  ```
- **CDSE credentials**: Copernicus Data Space Ecosystem OIDC client ID and secret, registered against your own CDSE account. The Solution uses these to authenticate against the [openEO federation](https://openeofed.dataspace.copernicus.eu/) and pull Copernicus data.
- **The Solution's EOAP package**: downloaded from a GitHub release.

## Step-by-step: running SAV locally

1. **Download and extract the EOAP package**

Each Solution is published as a `.tar.gz` release asset on GitHub. For SAV:

```bash
curl -LO https://github.com/PEOPLE-ECCO/ecco-marine-solutions/releases/download/v1.0.0/dhi-sav-v1.0.0.tar.gz
mkdir dhi-sav-v1.0.0 && tar -xzf dhi-sav-v1.0.0.tar.gz -C dhi-sav-v1.0.0
cd dhi-sav-v1.0.0
```

The package contains the CWL workflow (`run.cwl`), example parameter files (`sav_run_parameters_malaysia.json`, `sav_run_parameters_PEI.json`), and an `example.env` template. Other Solution releases can be found under their repository's `/releases` page, e.g. [ecco-marine-solutions/releases](https://github.com/PEOPLE-ECCO/ecco-marine-solutions/releases).

2. **Set your CDSE credentials as environment variables**

Copy `example.env` to `.env` and fill in your own CDSE OIDC client credentials, or export them directly:

```bash
export OPENEO_AUTH_CLIENT_ID="<your CDSE client id>"
export OPENEO_AUTH_CLIENT_SECRET="<your CDSE client secret>"
```

3. **Choose or edit a parameters file**

The package ships ready-to-use examples, e.g. `sav_run_parameters_malaysia.json`:

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
    "area_name": "Malaysia"
}
```

`rangestart`/`rangeend` define the temporal extent, `spatial_extent` the area of interest (a bbox or GeoJSON polygon), and `maxcloudcover` a cloud-cover threshold. Note that the SAV model is only trained for two regions, Malaysia and PEI (Prince Edward Island) – pick `sav_run_parameters_malaysia.json` or `sav_run_parameters_PEI.json` as a starting point and keep any custom extent close to one of these two areas.

4. **Run the CWL workflow**

```bash
cwl-runner run.cwl \
    --cdse_client_id=${OPENEO_AUTH_CLIENT_ID} \
    --cdse_client_secret=${OPENEO_AUTH_CLIENT_SECRET} \
    --parameters sav_run_parameters_malaysia.json \
    --run_name sav_run
```

`cwltool` pulls the published `ghcr.io/people-ecco/dhi-sav:latest` image, starts the container, which authenticates against CDSE, pulls the required Sentinel-2 data via openEO, and runs the SAV algorithm for the given extent and time range.

5. **Inspect the results**

Once the run finishes, results are written to `sav_run/output/`, including:

- `collection.json` – a STAC `ItemCollection` describing the outputs, with run metadata (`solution`, `solutionVersion`, `cdseVersion`, `inputParameters`, `executionDateTime`).
- Result rasters (e.g. `*_aggregated_SAV_probability.tif`, `*_aggregated_classification.tif`) and a GeoJSON file.
- `openeo_logs.txt` – logs from the underlying openEO job.

These files are self-contained and can be inspected locally (e.g. in QGIS) without any further platform involvement.

## Integration with ESA APEx

The OGC Best Practice for Earth Observation Application Packages (APEx, OGC BP 20-089) standardizes CWL/Docker/STAC-based packaging for portability across processing platforms. PEOPLE-ECCO Solutions share the same building blocks: CWL workflows, Docker containers, STAC-described outputs.

APEx supports the integration of algorithms into their central [Algorithm Catalogue](https://algorithm-catalogue.apex.esa.int/). Full OGC APEx compatibility is a current work task of the PEOPLE-ECCO and APEx teams. Once the Solutions are availble via the Catalogue, this section will be updated.
