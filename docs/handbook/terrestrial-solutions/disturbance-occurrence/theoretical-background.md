# 1. INTRODUCTION

This user guide provides the theoretical background for the Disturbance Occurrence tool used in PEOPLE-ECCO terrestrial workflows. The method is designed to detect abrupt vegetation disturbance signals from multi-year spectral index time series using piecewise linear break detection.

The approach builds on methodologies used in large-area time-series analysis for forest monitoring and change characterization, adapted here for broad ecosystem conservation applications where disturbance may occur in forests, woodlands, shrublands, and grasslands.

The key objectives of the Disturbance Occurrence tool are to:

1. Detect where a significant disturbance signal occurred.
2. Estimate when the dominant disturbance occurred.
3. Quantify disturbance magnitude, duration, and rate of change.
4. Provide metrics that can feed into prioritization and effectiveness workflows.

## 1.1 DISTURBANCE OCCURRENCE IN CONSERVATION MONITORING

Many conservation landscapes experience repeated and spatially heterogeneous disturbance from human land use and natural processes. Disturbance can include land clearing, shifting cultivation, repeated burning, and other abrupt vegetation transitions.

Conventional land cover maps often underrepresent short-duration or small-patch disturbances, especially where seasonal vegetation dynamics are strong. Time-series spectral methods provide a stronger basis for detection because they evaluate trajectory change through time rather than a single-date classification.

In PEOPLE-ECCO use cases, this disturbance signal is primarily used as:

- a direct layer for tracking disturbance occurrence through time, and
- an input to higher-level tools such as Habitat Disturbance Rating assessments.

## 1.2 WHY TRAJECTORY BREAK DETECTION

The Disturbance Occurrence tool uses a spectral trajectory break detection approach rather than direct binary classification. This design has several advantages:

- It is interpretable: outputs are explicit change metrics (year, magnitude, duration, rate).
- It separates stable and changing trajectory segments in a transparent way.
- It can be implemented in scalable pixel-wise processing pipelines.
- It supports integration with user-defined thresholds and validation workflows.

This approach is conceptually aligned with temporal segmentation methods such as LandTrendr and with broader change-detection families reviewed in the PEOPLE-ECCO ATBD, while remaining lightweight and adaptable for operational workflows.

## 1.3 SPECTRAL TRAJECTORIES AND CHANGE SIGNALS

Let a pixel time series be represented by year values x and spectral index values y (for example NBR or SAVI from annual or seasonal composites). The algorithm approximates the trajectory as a sequence of linear segments:

$$
y_t \approx \beta_k x_t + \alpha_k, \quad t \in \text{segment } k
$$

where each segment k has slope $\beta_k$ and intercept $\alpha_k$.

Abrupt disturbance is represented by segments with strong negative evolution (negative slope over a period), and by corresponding negative magnitude change between segment endpoints.

## 1.4 BREAK DETECTION METHOD USED IN THE TOOL

The implementation uses a Bottom-Up piecewise linear segmentation workflow. The method follows these main steps.

### Step 1. Build initial fine-scale segments

The input trajectory is split into two-point segments at the finest scale. If the number of observations is odd, the first observation is removed to enforce pairwise segmentation.

### Step 2. Compute merge costs

For each pair of adjacent segments, the algorithm computes the sum of squared errors (SSE) of a single linear fit over the merged interval.

Given a candidate merged interval with n observations, SSE is computed as:

$$
\text{SSE} = \hat{\sigma}^2 (n-1)
$$

where $\hat{\sigma}$ is the standard error from linear regression on that interval.

### Step 3. Iteratively merge the cheapest neighbors

At each iteration, the algorithm merges the adjacent pair with the smallest merge cost. Merging continues until either:

- no further merge candidate exists, or
- the minimum merge cost exceeds the user-defined threshold.

The threshold is therefore a structural complexity control for segmentation.

### Step 4. Estimate segment parameters

For each final segment, slope and intercept are estimated. For single-interval segments, line parameters are computed directly from the two endpoints.

### Step 5. Insert interpolated breakpoint connectors

The implementation inserts short interpolated connecting segments between adjacent fitted segments. This produces a continuous piecewise representation and supports subsequent metric extraction.

### Step 6. Extract disturbance metrics from segment set

The algorithm identifies the segment with the strongest negative evolution (minimum slope) as the dominant disturbance segment. It then derives:

- pre-change metrics from the previous segment (if present),
- change metrics from the dominant disturbance segment,
- post-change metrics from the following segment (if present).

## 1.5 OUTPUT METRICS

The tool returns per-pixel metrics with the following core fields:

- PreChgDur: pre-change duration ($end\_year - start\_year$)
- PreChgMag: pre-change magnitude (fitted segment end value minus fitted segment start value)
- PreChgEvl: pre-change evolution (segment slope)
- ChgYr: disturbance year (end year of the dominant negative-slope segment)
- ChgDur: disturbance duration ($end\_year - start\_year$)
- ChgMag: disturbance magnitude (fitted segment end value minus fitted segment start value)
- ChgEvl: disturbance evolution (segment slope)
- PstChgDur: post-change duration ($end\_year - start\_year$)
- PstChgMag: post-change magnitude (fitted segment end value minus fitted segment start value)
- PstChgEvl: post-change evolution (segment slope)

For segments that do not exist (for example no pre-change or post-change segment), the corresponding metrics remain NaN.

These outputs can be directly mapped, summarized by management units, or thresholded to derive binary disturbance occurrence products.

## 1.6 FROM METRICS TO DISTURBANCE OCCURRENCE PRODUCTS

The theoretical outputs are continuous metrics, while many conservation decisions require categorical layers. A common operational step is to apply a disturbance threshold to ChgMag (and optionally duration/year constraints) to classify pixels as disturbance occurrence for a target period.

This threshold is context dependent and should be calibrated with local knowledge and visual validation against very high resolution imagery or independent disturbance indicators.

# 2. ACKNOWLEDGEMENTS AND REFERENCES

Cardille, J. A., Perez, E., Crowley, M. A., Wulder, M. A., White, J. C., & Hermosilla, T. (2022). Multi-sensor change detection for within-year capture and labelling of forest disturbance. Remote Sensing of Environment, 268, 112741. https://doi.org/10.1016/j.rse.2021.112741

Hermosilla, T., Wulder, M. A., White, J. C., Coops, N. C., Hobart, G. W., & Campbell, L. B. (2016). Mass data processing of time series Landsat imagery: Pixels to data products for forest monitoring. International Journal of Digital Earth, 9(11), 1035-1054. https://doi.org/10.1080/17538947.2016.1187673

Kennedy, R. E., Yang, Z., & Cohen, W. B. (2010). Detecting trends in forest disturbance and recovery using yearly Landsat time series: 1. LandTrendr - Temporal segmentation algorithms. Remote Sensing of Environment, 114(12), 2897-2910. https://doi.org/10.1016/j.rse.2010.07.008

Kennedy, R. E., Yang, Z., Gorelick, N., Braaten, J., Cavalcante, L., Cohen, W. B., & Healey, S. (2018). Implementation of the LandTrendr Algorithm on Google Earth Engine. Remote Sensing, 10(5), 691. https://doi.org/10.3390/rs10050691

Keogh, E., Chu, S., Hart, D., & Pazzani, M. (2001). An online algorithm for segmenting time series. Proceedings 2001 IEEE International Conference on Data Mining.

Kavlin-Castaneda, M., Dean, A., Munk, M., Van doninck, J., Bijker, W., Rieke, M., & Willemen, L. (2025). PEOPLE-ECCO Requirement Baseline. Zenodo. https://doi.org/10.5281/zenodo.15396616

Xian, G. Z., Smith, K., Wellington, D., Horton, J., Zhou, Q., Li, C., Auch, R., Brown, J. F., Zhu, Z., & Reker, R. R. (2022). Implementation of the CCDC algorithm to produce the LCMAP Collection 1.0 annual land surface change product. Earth System Science Data, 14(1), 143-162. https://doi.org/10.5194/essd-14-143-2022

Zhu, Z., & Woodcock, C. E. (2014). Continuous change detection and classification of land cover using all available Landsat data. Remote Sensing of Environment, 144, 152-171.

