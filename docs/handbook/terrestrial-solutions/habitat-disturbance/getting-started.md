# Getting Started: Habitat Disturbance Rating

The Habitat Disturbance Rating workflow combines:

- Vegetation Disturbance Occurrence breaks
- Fire point occurrence
- Built-area pressure

and returns a zone-based disturbance score (`disturbance_index`).

This page describes the local CWL-style run approach using the files in `ecco-terrestrial-solutions`.

## Requirements

- Docker
- A CWL runner (`cwl-runner` / `cwltool`)
- Local clone of `https://github.com/PEOPLE-ECCO/ecco-terrestrial-solutions`
- Zone polygons
- Breaks raster
- Fire points
- Built raster

All layers should use compatible CRS and valid geometry.

## Step-by-step: preparing the disturbance index job

1. Clone and enter the repository

```bash
git clone https://github.com/PEOPLE-ECCO/ecco-terrestrial-solutions.git
cd ecco-terrestrial-solutions
```

1. Prepare a CWL job file

Start from `VDO_disturbance_index/disturbance_index-job.yml` and update:

- `zones_polys`
- `breaks_raster`
- `fires_points`
- `built_raster`
- `output`

You can also tune:

- `mmu_area`
- `majority_filter_size`
- `connectivity`
- `weights`
- `cap_percentile`
- `magnitude_threshold`

Example `disturbance_index-job.yml`:

```yaml
zones_polys: /path/to/zones.geojson
breaks_raster: /path/to/breaks.tif
fires_points: /path/to/firms_fire_points.geojson
built_raster: /path/to/built_areas.tif
output: /path/to/disturbance_index_output.geojson
mmu_area: 5000.0
majority_filter_size: 7
connectivity: 8
weights: "0.3,0.7,0.9"
cap_percentile: 99.0
magnitude_threshold: -200.0
use_opencv: true
```

Weight order is `disturbance_count,fire_count,built_sum`.

## Step-by-step: run with CWL

Run from the repository root:

```bash
cwltool cwl/disturbance_index.cwl VDO_disturbance_index/disturbance_index-job.yml
```

Note: `cwl/disturbance_index.cwl` uses an inline JavaScript expression, so `cwltool` may require a local Node.js runtime.

If your environment cannot run this CWL directly, use the included Docker wrapper as fallback:

```bash
docker build -f tooling/disturbance-index/disturbance_index.Dockerfile -t disturbance-index:latest .
bash VDO_disturbance_index/disturbance_index_docker_run.sh
```

## Output

The configured output file (GeoJSON/GPKG/Shapefile) includes at least:

- `disturbance_count`
- `fire_count`
- `built_sum`
- `disturbance_index`

## End-to-end sequence with upstream tools

1. Create BAP composites (`cwl/bap.cwl`).
1. Run Breaks / Disturbance Occurrence (`cwl/breaks.cwl`).
1. Use Breaks output raster plus fire and built layers in Habitat Disturbance Rating.

## Related pages

- [Theoretical Background](theoretical-background.md)
- [Key Considerations](key-considerations.md)