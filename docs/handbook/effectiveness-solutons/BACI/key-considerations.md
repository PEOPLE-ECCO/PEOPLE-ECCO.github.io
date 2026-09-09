# Key considerations when using the PEOPLE-ECCO tool

This page gives an overview of some key considerations necessary to run the BACI solution:

1. Defining units of analysis
2. Control-impact matching \
2.1. Selecting matching covariates \
2.2. Selecting matching parameters \
2.3. Evaluation of matching results \
3. Impact evaluation

## 1. Defining units of analysis

The first critical design consideration in counterfactual impact evaluation is definition the spatial unit and scale of analysis. The appropriate scale of analysis entirely dependents on the questions that are addressed. 
Defining the units of analysis therefore required thorough understanding of the interventions being assessed and the environment in which they take place. E.g., to assess conservation of restoration actions on individual land plots, the cadastral parcel (polygon) can be used as spatial unit of analysis. Conversely, a large protected area may cover various habitat types, with expected conservation effects varying spatially. In such situations, it is likely undesirable to treat the entire protected area as a single unit of analysis, but subdivisions of the protected area (points or polygons) can be compared to similar units outside the protected area.


Definition of impact and control units must account for real-world complexity such as positive spillover, 
e.g., when effects of protected areas on species abundance extend beyond the strict intervention, and negative spillover (leakage), 
e.g., when conservation interventions push deforestation to adjacent forests. Positive and negative spillover is often addressed by excluding buffer zones around the intervention area from analysis. 

Impact and (candidate) control units are to be provided by the user as a point or polygon vector file.

## 2. Control-impact matching

The goal of statistical matching is to eliminate the effect of confounders: external variables systematically associated with the allocation of a treatment (i.e. where a conservation or restoration intervention occurs) and the outcome of interest. 
Control-impact matching consists of three main steps: the selection of matching covariates, the selection of distance metric and matching method and the evaluation of the matching results.

### 2.1. Selecting matching covariates
Since the selection of covariates depends on the application, there is no fixed set of matching covariates to include and expert knowledge is required. Nevertheless, some general rules apply: 

* Matching analysis should include a set of covariates that are likely to impact, directly or indirectly, the selection of the treatment and the outcome of interest
* Matching covariates should themselves not be affected by the intervention
* Time-variant should typically only be included if they predate the intervention
* In case of doubt, it is better to err on the side of caution and include the variable

Common matching variables that can easily be obtained from open source geospatial data layers include:

* Topography: The [Copernicus DEM](https://dataspace.copernicus.eu/explore-data/data-collections/copernicus-contributing-missions/collections-description/COP-DEM) provides global coverage of elevation at 30 m and 90 m resolution. Terrain slope and aspect (the latter typically converted to northness and eastness) can be relevant for certain applications.
* Bioclimatic variable: [WorldClim](https://www.worldclim.org/data/bioclim.html) and [CHELSA](https://www.chelsa-climate.org/datasets/chelsa\_bioclim) provide kilometer-scale bioclimatic variables that can be especially relevant for studies over large geographic extents.
* Places, roads, waterways: [OpenStreetMap](https://www.openstreetmap.org) contains datasets such as settlements and roads. The distance to these features is often indicative of human pressures, e.g., facilitating timber extraction and increasing deforestation pressure.

### 2.2. Selecting matching parameters

Several **distance** metrics for matching exist, and it can be useful to evaluate the outcome and validation of several matching approaches (see 2.3.). 
Common matching methods in conservation science are propensity score matching and Mahalanobis matching. 

#### When **propensity score** matching can be preferable 
* High-dimensional covariates - propensity score summarizes in single metric
* Covariates include categorical variable
* Goal is estimation of population-level effect (e.g., pooled results over several sites) rather than individual pairings between units of analysis

#### When **Mahalanobis** matching can be preferable
* Low-dimensional categorical variables
* Tight relationships between control and impact units of analysis are required

Other matching parameters include the **ratio** of control units per impact unit, and whether to **replace** matched control units so that a control can be paired with several impact units.

#### Ratio
Typically, use `ratio=1` only when performing impact assessment at the population level, as at individual unit of analysis level this does not allow to estimate significance. Increasing the ratio can lead to stronger significance of impact assessment results, but may lead to environmentally different units being paired.

#### Replace
Setting `replace=TRUE` is appropriate when `ratio>1` and the number of control units is not much larger than the number of impact units. Replacement should typically not be used when impact assessment is performed at the population (pooled units of analysis) level.


### 2.3. Evaluation of matching results

Matching evaluation can be done by inspecting the similarity between the covariates of impact and control units after matching. 
If large differences between impact and control units in the distance metric or matching covariates persist after matching, it may be advisable to repeat matching with a different set of distance metric and matching method.
Absolute Standardized Mean Difference smaller than 0.1 are typically considered as indicative of adequate matching. 
Distribution of impact and control should show sufficient overlap. Caution is advised on interpretation of consecutive impact evaluation results if covariate overlap is low or absolute Standardized Mean Difference is high

## 3. Impact evaluation

Impact evaluation can be based on a before-after-control-impact or control-impact design. 
The contrast metric will be negative if the effect (or difference `after-before`) is larger in the impact units than in the control units.
If reforestation interventions are assessed using a fractional tree cover metric in before and after period, negative values of the BACI contrast mean that tree cover increase more in impact units compared to control units.

