# Key Considerations for Non-Expert Users

This page explains the main decisions needed to run and interpret the Habitat Disturbance Rating tool.

The workflow combines three disturbance pressure factors into one score:

1. Vegetation Disturbance-break pixels (`breaks_count`)
2. Fire Occurrence (NASA FIRMS) (`fire_count`)
3. Built-area pressure (`built_sum`)

You do not need to be a remote sensing specialist to use the tool effectively. A practical approach is to start with defaults, validate in known areas, and then tune parameters.

## 1. Choosing the Spatial Analysis Units

The output score is calculated per polygon zone.

Recommended practice:

- Use zones aligned with your decision context (for example management units, watersheds, or fixed grids).
- Keep zone size consistent across the study area to support comparison.
- Ensure zone geometry is valid and in a projected CRS suitable for area-based filtering.

If zones are too small relative to raster resolution, scores may become noisy.

## 2. Understanding the Three Input Factors

### 2.1 Vegetation Disturbance Occurrence (`breaks_count`)

This factor counts retained break pixels per zone after thresholding and spatial filtering.

- Strength: captures vegetation disturbance patterns from time-series change analysis.
- Watch out: sensitive to preprocessing quality and threshold choice.

### 2.2 Fire pressure (`fire_count`)

This factor counts fire points inside each zone.

- Strength: clear indicator of recurrent fire pressure.
- Watch out: point-density depends on source characteristics and temporal coverage.

### 2.3 Built pressure (`built_sum`)

This factor sums built-area raster values in each zone.

- Strength: captures human settlement/infrastructure pressure.
- Watch out: interpretation depends on how built raster values are encoded.

## 3. Key Parameters and Their Effects

### 3.1 Vegetation Disturbance Occurrence break magnitude threshold (default `-200`)

Controls which pixels are retained as a Vegetation Disturbance Occurrence.

- More negative threshold: stricter, fewer pixels retained.
- Less negative threshold: more inclusive, more pixels retained.

Tune this using local examples of known disturbance.

### 3.2 Majority filter size (default `7`)

Controls smoothing of break-year raster.

- Larger kernel: stronger smoothing, fewer isolated artifacts.
- Smaller kernel: preserves local detail but can keep noise.

Use odd values only.

### 3.3 MMU area (default `5000`) and connectivity (default `8`)

Controls minimum retained patch size.

- Larger MMU: removes more small patches.
- Smaller MMU: keeps more local features.
- Connectivity `8` is more permissive than `4` and usually retains diagonally connected patches.

Set MMU using map scale and minimum feature size relevant to your conservation application.

### 3.4 Cap percentile (default `99`)

Controls normalization ceiling after `log1p` transform.

- Higher percentile: less clipping of high values.
- Lower percentile: stronger compression of extremes.

This parameter changes sensitivity to outliers in high-pressure zones.

### 3.5 Factor weights

Default factor weights are:

- `fire_count=0.3`
- `breaks_count=0.7`
- `built_sum=0.9`

The workflow rescales these to sum to 1 before calculating the final score.

Important:

- Keep all three factors in the weight string.
- Use non-negative values.
- Larger weight means stronger influence on the final rating.

## 4. Data Quality and CRS Considerations

All inputs should be quality-checked before running:

- Vegetation Disturbance Occurrence breaks raster should have expected two-band structure (year, magnitude).
- Fire points should have valid point geometries.
- Built raster should be co-registered and spatially aligned with analysis extent.
- Zone, point, and raster CRS should be compatible for accurate spatial operations.

The implementation reprojects rasters to zone CRS where needed, but quality and consistency of source datasets remain critical.

## 5. Interpreting the Output Score

The output field is currently named `disturbance_index`, but it represents the Habitat Disturbance Rating score.

- Score range is 0 to 1.
- Higher score indicates higher combined disturbance pressure relative to other zones in the analysis.
- The score is comparative within a run and parameterization, not an absolute ecological threshold.

Interpret scores together with component fields (`breaks_count`, `fire_count`, `built_sum`) to understand why a zone is high or low.

## 6. Validation and Calibration Workflow

Recommended workflow:

1. Run defaults on a small test area.
2. Inspect intermediate rasters (`VDO.filtered_years.tif`, `VDO.mmu_filtered.tif`).
3. Compare high/low score zones with local knowledge or very high resolution imagery.
4. Adjust Vegetation Disturbance Occurrence break threshold, MMU, and weights.
5. Re-run and check whether ranking of known high-pressure zones improves.

Always document parameter settings used for each operational run.

## 7. Common Pitfalls to Avoid

- Using weights that omit required factor names.
- Interpreting the score as absolute without considering relative scaling and chosen extent.
- Running without checking that Vegetation Disutrbance Occurrence break magnitude threshold fits the selected spectral index.
- Using zone units that are too small for the raster resolution.
- Skipping inspection of intermediate filtering outputs.

## 8. Simple Decision Workflow

1. Define the management question and choose analysis zones.
2. Prepare and quality-check the three input factors.
3. Run with defaults and save intermediate outputs.
4. Validate against known disturbed and relatively intact areas.
5. Tune threshold, MMU, and weights to match local context.
6. Produce final Habitat Disturbance Rating map and keep parameters for reproducibility.

