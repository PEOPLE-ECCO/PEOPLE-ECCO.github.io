# Getting Started with Best-Available Pixel

This page gives a practical quick start for running the PEOPLE-ECCO Best-Available Pixel (BAP) implementation and understanding what it produces.

## 1. What BAP does

BAP builds one composite per requested period (yearly or monthly) by ranking candidate Sentinel-2 observations per pixel with three score components:

1. distance-to-cloud score,
2. date score,
3. cloud-coverage score.

The final score is a weighted average:

$$
Score = \frac{w_{dtc} DTC + w_{date} DATE + w_{cov} COV}{w_{dtc}+w_{date}+w_{cov}}
$$

Default weights in the current implementation are:

- $w_{dtc}=1.0$
- $w_{date}=0.8$
- $w_{cov}=0.5$

These are configurable with:

- `score_weight_dtc`
- `score_weight_date`
- `score_weight_coverage`

## 2. Pick the right profile first

Set `export_profile` according to your downstream workflow:

- `spectral_recovery`: yearly periods and reflectance outputs.
- `seasonal_sen`: monthly periods and index outputs.
- `breaks`: yearly periods and a single index band per year.
- `custom`: keeps your explicit compositing and payload settings.

## 3. Key runtime parameters

Minimum inputs:

- `spatial_extent` (GeoJSON FeatureCollection/Feature) or `spatial_extent_file`
- `years`

Most important quality controls:

- `max_cloud_cover` (scene pre-filter)
- `exclude_scl_classes` (pixel-level exclusions)
- `cloud_buffer_px` (buffer around cloud/shadow pixels)
- `dtc_max_distance` (distance-to-cloud response scale)
- score weights (`score_weight_dtc`, `score_weight_date`, `score_weight_coverage`)

## 4. What gets written

Each run writes GeoTIFF outputs plus a manifest (`bap_manifest.json`) in the output directory.

For each output, the manifest includes at least:

- period label and temporal extent,
- export profile and payload,
- selected reflectance bands and/or indices,
- spatial resolution,
- score weights used for ranking.

## 5. Suggested first run

1. Start with defaults and a small AOI.
2. Run one year first (or one season worth of months).
3. Visually check cloud-edge artifacts and coverage.
4. Tune cloud buffer and score weights only if needed.
5. Scale to full AOI set after quality checks pass.

## 6. Tutorial placeholders

The sections below are placeholders for step-by-step tutorials that can be expanded with screenshots and example parameter files.

### 6.1 Run BAP from CLI (placeholder)

Planned tutorial scope:

1. Prepare credentials and environment variables.
2. Prepare a run parameters JSON file.
3. Run the CLI command.
4. Verify outputs and the manifest.
5. Troubleshoot common runtime errors.

### 6.2 Run BAP in the PEOPLE-ECCO platform (placeholder)

Planned tutorial scope:

1. Open the BAP workflow in the platform interface.
2. Upload or select AOI input.
3. Configure profile, temporal settings, and quality controls.
4. Launch the run and monitor job status.
5. Download and validate output composites.