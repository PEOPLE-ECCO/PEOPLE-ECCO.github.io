# Key Considerations for Non-Expert Users

This page explains the main decisions needed to run and interpret the Vegetation Disturbance Occurrence tool.

The method uses a spectral trajectory break detection workflow. In practical terms, your main decisions are:

1. Which spectral index and compositing period to use.
2. Which analysis period to evaluate.
3. Which segmentation threshold to use.
4. How to convert continuous metrics into disturbance classes.
5. How to validate and interpret the outputs.

You do not need to be a remote sensing specialist to start. Begin with a simple baseline setup, validate against known locations, and refine settings iteratively.

## 1. Choosing the Input Time Series

### 1.1 Use consistent seasonal composites

The tool is most stable when each year is represented by a composite of a comparable seasonal window (for example peak vegetation months). This reduces potential for false disturbance signals caused by seasonal mismatch.

Recommended practice:

- Focus on high-quality cloud-masked composites, and if necessary adjust BAP composite input parameters to achieve a cloud-free composite.
- Keep compositing logic unchanged across years and use the same input months for the composite every year.

### 1.2 Choose a spectral index that matches your objective

Common options:

- NBR: often strong for disturbance and woody vegetation change.
- SAVI: useful where exposed soil is frequent.
- NDVI: useful for broad greenness trends, but can saturate in dense vegetation.

If possible, test multiple indices to compare whether identified disturbance areas are consistent.

## 2. Choosing the Analysis Period

The algorithm expects a yearly sequence between a start year and end year. Choose a period long enough to capture:

- pre-disturbance baseline behavior,
- disturbance phase,
- and post-disturbance behavior.

As a practical minimum, the process requires a minimum of two years of data prior to the year of disturbance detection (a total of three years). A longer period generally improves the temporal segment/trajectory building.

## 3. Choosing the Segmentation Threshold

In the current implementation, the threshold controls when neighboring segments stop being merged (based on merge SSE). This is a complexity control, not a disturbance/no-disturbance threshold.

### 3.1 How threshold affects results

- Lower threshold: fewer merges, more segments, higher sensitivity to small trajectory changes.
- Higher threshold: more merges, fewer segments, smoother trajectories, lower sensitivity.

### 3.2 Practical tuning workflow

1. Start with default threshold used by the tool configuration.
2. Run a small test area with known disturbance examples.
3. Compare outputs with a lower and higher threshold.
4. Select the value that best balances omission and false positives.

## 4. Data Quality Rules

The disturbance detection code assumes clean numerical time series per pixel.

Critical rules:

- Missing values must be removed or masked before running the algorithm.
- Residual cloud and shadow contamination can create false disturbance detection.
- Sensor harmonization and consistent preprocessing are important in multi-year analyses.

If data quality is inconsistent, prioritize improving preprocessing before adjusting algorithm thresholds.

## 5. Interpreting the Outputs

The tool outputs continuous change metrics. The most important fields for disturbance occurrence mapping are:

- ChgYr: year of dominant disturbance segment.
- ChgMag: magnitude of dominant disturbance.
- ChgDur: duration of dominant disturbance segment.
- ChgEvl: rate of change (slope) during disturbance segment.

Additional fields (PreChg and PstChg) help interpret trajectory context before and after disturbance.

### 5.1 From metrics to categorical disturbance maps

Most users will apply thresholds to continuous metrics to classify disturbance occurrence.

Typical logic:

- Require disturbance magnitude beyond a chosen threshold.
- Optionally constrain disturbance year to a target monitoring period.
- Optionally constrain duration to remove very short noisy events.

Thresholds must be calibrated to landscape type and selected spectral index.

## 6. Validation and Quality Control

Use local knowledge and independent observations to validate outputs.

Recommended checks:

- Visual comparison with high-resolution imagery.
- Cross-comparison with fire detections where relevant.
- Spatial consistency checks (for example isolated single-pixel artifacts).
- Temporal plausibility checks (does change year align with known events).

If results are noisy, revisit compositing and masking first, then threshold tuning.

## 7. Common Pitfalls to Avoid

- Treating segmentation threshold as a direct disturbance threshold.
- Running the tool on short or inconsistent time series.
- Using non-comparable seasonal windows across years.
- Interpreting disturbance from one metric alone without checking year and duration.
- Skipping local validation before operational use.

## 8. Simple Decision Workflow

1. Define your disturbance question and monitoring period.
2. Build consistent yearly composites and select index (start with NBR).
3. Run with default segmentation threshold.
4. Review ChgYr, ChgMag, ChgDur together.
5. Calibrate disturbance classification thresholds using known sites.
6. Validate with independent data and refine.
7. Scale to full area after settings are stable.

