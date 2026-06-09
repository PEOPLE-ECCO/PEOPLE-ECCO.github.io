# Key Considerations for Best-Available Pixel

This page provides practical guidance for configuring Best-Available Pixel (BAP) runs and interpreting outputs reliably.

## 1. Start with clear compositing goals

Before setting parameters, define what your composite should optimize:

- Maximum spatial completeness (fewer gaps).
- Minimum cloud and haze contamination.
- Consistent temporal representation across years.

Different goals can require different trade-offs between strict masking and usable coverage.

### Choose the downstream algorithm mode first

Before fixing BAP parameters, decide which downstream algorithm version/mode the composites are for:

- Spectral Recovery mode: yearly composites, typically reflectance-focused outputs.
- Seasonal Sen's slope mode: monthly composites, typically index-focused outputs.

In practice, this is a key design decision because temporal granularity and export payload should match the downstream method you plan to run.

## 2. Temporal window design is critical

BAP ranks observations within a defined period. The period you choose strongly shapes results.

Use monthly windows when:

- Phenology is strong and month-to-month differences matter.
- You need seasonal consistency for trend analysis.

Use yearly windows when:

- You need a broad annual summary.
- Monthly observation density is too low.

Practical tip:

- In strongly seasonal or snow-affected regions, exclude months that are not ecologically comparable.

## 3. Cloud controls: strictness vs coverage

Three settings control this balance:

- max_cloud_cover: scene-level prefilter.
- exclude_scl_classes: pixel-level exclusions from SCL classes.
- cloud_buffer_px: extra safety margin around cloud/shadow pixels.

If masks are too strict:

- Output can become spatially sparse.

If masks are too permissive:

- Residual cloud contamination can remain.

Recommended approach:

1. Start with default exclusions and a modest cloud buffer.
2. Inspect a few representative tiles and months.
3. Adjust one parameter at a time.

## 4. Distance-to-cloud behavior matters

The distance-to-cloud term is one of the strongest ranking signals. The dtc_max_distance setting controls how quickly score improves as distance from clouds increases.

- Larger values are more conservative near clouds.
- Smaller values can increase usable pixels but may allow more edge artifacts.

Tune this according to local cloud dynamics and required product cleanliness.

## 5. Understand the default score weighting

The implementation combines:

- distance-to-cloud,
- date proximity,
- area coverage,

with default relative weights of 1.0, 0.8, and 0.5.

These weights are configurable and can be modified for your workflow needs; the values above are defaults, not fixed constants.

Implication:

- Cloud proximity and date proximity are prioritized over pure scene coverage.

If your use case prioritizes completeness over strict quality, adjust the weights and document the final choice for reproducibility.

## 6. AOI geometry quality is non-negotiable

BAP is sensitive to geometry validity because masking and clipping happen at pixel level.

Check before running:

- Null or empty geometries.
- Invalid polygons.
- CRS mismatches.

Poor geometry quality can look like data-quality problems even when the ranking logic is correct.

## 7. Resolution and runtime trade-offs

Higher spatial resolution and long temporal windows increase processing load and backend queue time.

For robust operations:

- Use resume mode to skip existing outputs.
- Consider tile-first workflows for many study areas in a contiguous location.
- Keep run manifests to support recovery after interruptions.

## 8. Validate outputs before downstream analysis

Do not assume that all composites are equally reliable.

Perform quick checks:

- Visual check for cloud-edge artifacts.
- Coverage consistency across periods.
- Outlier periods with unusually low valid data.

Only after quality checks should outputs be used for index trends, disturbance mapping, or recovery metrics.

## 9. Interpretation caveats

BAP improves observation quality, but it does not remove all uncertainty.

- Persistent cloud regimes may still produce lower-confidence composites.
- Phenology differences between years can remain if windows are not harmonized.
- Composite quality is not equivalent to ecological validity; field context is still essential.

## 10. Minimum reproducibility checklist

Record these settings for every production run:

- temporal window definition,
- selected years/months,
- max_cloud_cover,
- exclude_scl_classes,
- cloud_buffer_px,
- dtc_max_distance,
- score weights,
- spatial resolution,
- output manifest path.

This makes reruns and cross-site comparisons far more reliable.
