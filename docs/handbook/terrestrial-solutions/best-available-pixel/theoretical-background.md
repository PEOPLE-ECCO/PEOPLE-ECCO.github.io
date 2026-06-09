# 1. INTRODUCTION

The Best-Available Pixel (BAP) solution creates cleaner satellite composites by selecting, for each pixel, the most suitable observation within a defined time window. Instead of using a single image date, BAP evaluates many candidate observations and keeps the one with the highest quality score.

The PEOPLE-ECCO BAP implementation is derived from the openEO BAP approach available through the Copernicus Data Space Ecosystem (CDSE) Algorithm Plaza, and adapted for this workflow context.

This page explains:

1. Why pixel ranking is used.
2. How BAP scores are constructed.
3. How the final composite is assembled.
4. What this means for interpretation.

## 1.0 Operational modes and downstream algorithm version

When running BAP composites within the workflow of other PEOPLE-ECCO solutions, the compositing should be configured according to the downstream algorithm mode you intend to run:

- Spectral Recovery mode: BAP is typically run in yearly compositing mode to support annual trend workflows.
- Seasonal Sen's slope mode: BAP is typically run in monthly compositing mode to preserve intra-annual seasonal signals.

This mode selection is not only technical; it defines the temporal behavior of the composite and should be decided before parameter tuning.

## 1.1 Why Best-Available Pixel compositing is needed

In optical satellite time series, the same location is observed multiple times, often under very different viewing and atmospheric conditions. Clouds, cloud shadows, haze, snow, and adjacency effects can produce noisy or invalid measurements. If these observations are used directly, change analysis can become unstable.

BAP addresses this by replacing date-based selection with quality-based selection at pixel level. Each candidate observation is scored, and the best candidate is retained for each pixel. This generally improves spatial completeness and consistency of downstream products.

## 1.2 Data and candidate observations

In this implementation, BAP uses Sentinel-2 L2A imagery and its Scene Classification Layer (SCL).

- Candidate observations are gathered within a user-defined period (monthly or yearly profile).
- Invalid observations are flagged using cloud-related masks and excluded SCL classes.
- Cloud buffering is applied so pixels near cloud edges are treated conservatively.

Default excluded SCL classes are:

- 1: Saturated/defective
- 2: Dark area pixels
- 3: Cloud shadows
- 7: Unclassified
- 8: Cloud medium probability
- 9: Cloud high probability
- 10: Thin cirrus

## 1.3 BAP scoring model

For each candidate observation, three quality components are computed:

1. Distance-to-cloud score $DTC$: favors pixels farther from clouds.
2. Date score $DATE$: favors observations near the middle of the target period.
3. Coverage score $COV$: favors dates with higher cloud-free coverage over the area.

The final BAP score is a weighted average:

$$
Score = \frac{w_{dtc}\,DTC + w_{date}\,DATE + w_{cov}\,COV}{w_{dtc}+w_{date}+w_{cov}}
$$

With current default weights:

- $w_{dtc} = 1.0$
- $w_{date} = 0.8$
- $w_{cov} = 0.5$

These weights are modifiable in the implementation and should be treated as tunable parameters. The values above are defaults used in the current workflow.

So the operational expression is:

$$
Score = \frac{1.0\,DTC + 0.8\,DATE + 0.5\,COV}{2.3}
$$

### Date scoring intuition

Date score is computed with a Gaussian-like curve centered on day 15 of each month. This penalizes observations far from mid-month and reduces edge-date bias.

### Distance-to-cloud intuition

Distance-to-cloud is computed from a buffered cloud mask. In the current implementation, the cloud mask is based on SCL cloud/shadow classes (3, 8, 9, 10), then expanded with the configured cloud buffer. Pixels very close to cloud or cloud shadow receive lower scores to reduce residual contamination.

## 1.4 Rank selection and compositing

After scoring:

1. A rank mask identifies the highest-scoring candidate for each pixel in the temporal neighborhood.
2. Reflectance bands are masked with this rank mask.
3. A temporal reducer (median) is used to build the composite for the period.
4. The result is clipped to the area of interest when clipping is enabled.

In the current openEO graph, rank selection is computed with a monthly temporal neighborhood and the selected stack is then reduced with a yearly median inside each requested period.

This procedure is repeated per period (for example each selected month in each year), and outputs are written as GeoTIFF files with a manifest.

## 1.5 Relation to downstream indices

BAP itself is a compositing and quality-control stage. Spectral indices such as NDVI, NBR, NDMI, NBR2, SAVI, and TCW are computed from the BAP composite (or exported reflectance) and then used by downstream analyses.

This separation is important:

- BAP improves input data quality and temporal consistency.
- Trend or disturbance metrics then operate on cleaner inputs.

## 1.6 Strengths and limitations

Strengths:

- Better robustness to cloud contamination than single-date selection.
- Pixel-level flexibility in heterogeneous conditions.
- Compatible with large-area and long time-series workflows.

Limitations:

- Quality depends on availability of valid observations.
- Parameter choices (cloud buffer, SCL exclusions, weights) influence output behavior.
- In persistently cloudy periods, even best-ranked observations may still have residual noise.

# 2. ACKNOWLEDGEMENTS AND REFERENCES

- Copernicus Data Space Ecosystem (CDSE) Algorithm Plaza. Best Available Pixel compositing implementation and examples.
- openEO Community Examples. Rank Composites and BAP utilities. https://github.com/Open-EO/openeo-community-examples
- White, J. C., Wulder, M. A., Hobart, G. W., et al. (2014). Pixel-Based Image Compositing for Large-Area Dense Time Series Applications and Science. Canadian Journal of Remote Sensing, 40(3), 192-212.
- Francini, S., Hermosilla, T., Coops, N. C., et al. (2023). An assessment approach for pixel-based image composites. ISPRS Journal of Photogrammetry and Remote Sensing, 202, 1-12.
- Zhu, Z., Wang, S., and Woodcock, C. E. (2015). Improvement and expansion of the Fmask algorithm: Cloud, cloud shadow, and snow detection for Landsats 4-7, 8, and Sentinel 2 images. Remote Sensing of Environment, 159, 269-277.