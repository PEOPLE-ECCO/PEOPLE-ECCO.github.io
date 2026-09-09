# 1. INTRODUCTION

This user guide provides the theoretical background for the Habitat Disturbance Rating tool used in PEOPLE-ECCO terrestrial workflows.

The Habitat Disturbance Rating integrates multiple disturbance proxies into a single, normalized score per analysis unit (for example hexagons or other management polygons). The method is designed to support conservation prioritization and monitoring by combining:

1. Vegetation disturbance occurrence derived from spectral breaks.
2. Fire occurrence.
3. Built-area pressure.

The current implementation is aligned with the ATBD concept of a flexible, user-weighted disturbance metric.

## 1.1 DISTURBANCE RATING IN CONSERVATION CONTEXT

Habitat disturbance is rarely explained by one pressure layer alone. In many landscapes, patterns of ecological pressure emerge from the interaction of different stressors, such as vegetation clearing, fire recurrence, and settlement expansion.

A composite disturbance rating provides a practical decision layer because it:

- summarizes multiple stressors at a common reporting unit,
- supports comparison across large areas,
- can be tuned to local conservation objectives through user-defined weights,
- can be updated as new yearly disturbance inputs become available.

In PEOPLE-ECCO workflows, the Habitat Disturbance Rating can be used as:

- a direct prioritization layer for conservation planning,
- an input to landscape-level intactness analysis,
- a supporting layer for evaluating directional change in pressure over time.

## 1.2 INPUT DATA AND ECOLOGICAL RATIONALE

The implemented workflow uses three core factors per zone:

- `breaks_count`: count of Vegetation Disturbance Occurrence break pixels (after filtering),
- `fire_count`: count of fire points falling inside each zone,
- `built_sum`: zonal sum of built-area raster values.

### Vegetation Disturbance Occurrence breaks-based disturbance factor

The Vegetation Disturbance Occurrence breaks raster is expected as a two-band product:

- band 1: break year,
- band 2: break magnitude.

Pixels are retained where Vegetation Disturbance Occurrence break magnitude is less than or equal to a selected threshold (default: -200). This targets strong negative disturbance signals and excludes weak change.

### Fire factor

Fire points are spatially joined to zones and counted (`within` predicate). This captures local recurrence and concentration of fire activity as a disturbance pressure proxy.

### Built-area factor

Built-area intensity is summarized as zonal sum, providing a proxy for human footprint/settlement pressure inside each analysis unit.

## 1.3 SPATIAL FILTERING OF DISTURBANCE BREAKS

Before zonal summarization, the workflow applies two filtering stages to the retained break-year raster.

### Stage 1: Majority filter

A majority (mode) filter is applied with an odd kernel size (default: 7). This reduces pixel-level salt-and-pepper noise and favors local spatial consistency.

### Stage 2: Minimum mapping unit (MMU)

Connected patches smaller than a minimum area (default: 5000 in raster-CRS square units) are removed using connected-component labeling (default connectivity: 8-neighbor).

This stage suppresses small isolated artifacts and retains only spatially coherent disturbance patches.

## 1.4 NORMALIZATION AND RATING CALCULATION

After zonal extraction, each factor is transformed and normalized before weighted combination.

Let $x_{i,j}$ be factor $j$ for zone $i$. The workflow computes:

$$
z_{i,j} = \log(1 + x_{i,j})
$$

For each factor, a cap value $c_j$ is computed as the percentile (default 99th percentile) of $z_{:,j}$, then values are normalized and clipped:

$$
n_{i,j} = \min\left(1, \frac{z_{i,j}}{c_j}\right)
$$

User-provided weights $w_j$ are rescaled to sum to 1:

$$
\tilde{w}_j = \frac{w_j}{\sum_j w_j}
$$

The final rating score for each zone is:

$$
R_i = \sum_j \tilde{w}_j \cdot n_{i,j}
$$

By construction, $R_i \in [0,1]$, where larger values indicate higher composite disturbance pressure.

## 1.5 DEFAULT PARAMETERIZATION IN THE IMPLEMENTATION

The branch implementation defines these defaults:

- magnitude threshold: -200
- majority filter size: 7
- MMU area: 5000
- connectivity: 8
- cap percentile: 99
- factor weights (before rescaling):
	- fire_count = 0.3
	- breaks_count = 0.7
	- built_sum = 0.9

Although the implementation writes the output score to a field named `disturbance_index`, this field is the Habitat Disturbance Rating score described above.

## 1.6 OUTPUTS

Primary output:

- vector layer of zones with:
	- `breaks_count`
	- `fire_count`
	- `built_sum`
	- `disturbance_index` (Habitat Disturbance Rating score)

Optional intermediate outputs:

- `VDO.filtered_years.tif`
- `VDO.mmu_filtered.tif`
- `GHS_built_s.tif`
- `FIRMS_fires.gpkg`

These process outputs are useful for quality control and calibration.

## 1.7 ALIGNMENT WITH PEOPLE-ECCO ATBD

The method is consistent with the ATBD trade-off framing for disturbance characterization and prioritization:

- It integrates a key PEOPLE-ECCO disturbance product (breaks-based vegetation disturbance).
- It combines disturbance signals with human-pressure proxies.
- It is modular and user-configurable.
- It supports scalable spatial summarization and repeatable yearly updates.

The design prioritizes interpretability and operational flexibility over black-box modeling, which supports transparent communication with conservation practitioners.

# 2. ACKNOWLEDGEMENTS AND REFERENCES

Cardille, J. A., Perez, E., Crowley, M. A., Wulder, M. A., White, J. C., & Hermosilla, T. (2022). Multi-sensor change detection for within-year capture and labelling of forest disturbance. Remote Sensing of Environment, 268, 112741. https://doi.org/10.1016/j.rse.2021.112741

Grantham, H. S., Duncan, A., Evans, T. D., Jones, K. R., Beyer, H. L., Schuster, R., Walston, J., Ray, J. C., Robinson, J. G., Callow, M., Clements, T., Costa, H. M., DeGemmis, A., Elsen, P. R., Ervin, J., Franco, P., Goldman, E., Goetz, S., Hansen, A., ... Watson, J. E. M. (2020). Anthropogenic modification of forests means only 40% of remaining forests have high ecosystem integrity. Nature Communications, 11(1). https://doi.org/10.1038/s41467-020-19493-3

Hermosilla, T., Wulder, M. A., White, J. C., Coops, N. C., Hobart, G. W., & Campbell, L. B. (2016). Mass data processing of time series Landsat imagery: Pixels to data products for forest monitoring. International Journal of Digital Earth, 9(11), 1035-1054. https://doi.org/10.1080/17538947.2016.1187673

Kavlin-Castaneda, M., Dean, A., Munk, M., Van doninck, J., Bijker, W., Rieke, M., & Willemen, L. (2025). PEOPLE-ECCO Requirement Baseline. Zenodo. https://doi.org/10.5281/zenodo.15396616

Kennedy, R. E., Yang, Z., & Cohen, W. B. (2010). Detecting trends in forest disturbance and recovery using yearly Landsat time series: 1. LandTrendr - Temporal segmentation algorithms. Remote Sensing of Environment, 114(12), 2897-2910. https://doi.org/10.1016/j.rse.2010.07.008

Newbold, T., Hudson, L., Arnell, A., Contu, S., De Palma, A., Ferrier, S., Hill, S. L. L., Hoskins, A., Lysenko, I., Phillips, H., Burton, V., Chng, W. T. C., Emerson, S. R., Gao, D., Pask-Hale, G., Hutton, J., Jung, M., Sanchez-Ortiz, K., Simmons, B., ... Purvis, A. (2016). Global map of the Biodiversity Intactness Index, from Newbold et al. (2016) Science. Natural History Museum. https://doi.org/10.5519/0009936

