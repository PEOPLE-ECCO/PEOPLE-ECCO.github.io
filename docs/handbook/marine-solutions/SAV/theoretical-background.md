# Habitat Extent: Theoretical Background

## Ecological relevance

Submerged aquatic vegetation, coral reefs, and other benthic habitats provide ecosystem services including habitat provision, shoreline stabilization, nutrient cycling, and carbon sequestration. Their extent and distribution are important indicators for monitoring ecosystem condition and supporting conservation and restoration.

## Traditional mapping limitations

Field transects, quadrat surveys, aerial surveys, and video observations can provide high local accuracy but are resource-intensive, spatially limited, and difficult to repeat consistently at large scale.

## Earth Observation

Satellite remote sensing provides repeatable large-area coverage. Sentinel-2 offers spatial and spectral information that can distinguish major benthic habitat types under suitable conditions.

## Optical challenges

Submerged habitat signals are affected by:

- Water-column absorption and scattering
- Turbidity and suspended matter
- Water-depth variation
- Sun glint and surface conditions
- Cloud and haze

Optical mapping is most effective in shallow, clear water during periods with little surface disturbance.

## Spectral features and indices

Indices derived from blue, green, red, and near-infrared bands can enhance vegetation, water-penetration, and substrate-reflectance differences and provide interpretable classification predictors.

## Machine learning

Supervised methods such as Random Forest can combine spectral, spatial, and structural features and model non-linear relationships. They support scalable habitat classification within operational workflows.

## Compositing

Seasonal or annual composites combine multiple observations to reduce cloud contamination and noise and to represent stable environmental states without processing a complete time series.

## Operational workflow

The adopted workflow uses Sentinel-2 imagery, cloud-free selection and compositing, spectral and spatial feature extraction, supervised classification, and production of spatially explicit habitat maps. It balances scientific robustness, computational efficiency, and operational usability.

## References

- Huber, S. et al. (2022). [Novel approach to large-scale monitoring of submerged aquatic vegetation](https://academic.oup.com/ieam/article/18/4/909/7727157)
- Rowan, G.S.L. and Kalacska, M. (2021). [Review of remote sensing of submerged aquatic vegetation](https://www.mdpi.com/2072-4292/13/4/623)
- Lønborg, C. et al. (2022). [Overview of monitoring techniques for SAV](https://backend.orbit.dtu.dk/ws/files/262807068/ieam.4552.pdf)
- NASA (2023). [Spectral indices for land and aquatic applications](https://www.earthdata.nasa.gov/learn/trainings/spectral-indices-land-aquatic-applications)
- DHI (2024). [MCSAV platform overview](https://mcsav-malaysia.dhigroup.com/)
- UNDP Ocean Innovation Challenge (2025). [Mapping ecosystems with Copernicus imagery](https://dev.oceaninnovationchallenge.org/sites/default/files/2025-06/undp_oic_dhi_v4.pdf)
- Lyzenga, D. R. (1981). Remote sensing of bottom reflectance and water attenuation parameters in shallow water using aircraft and Landsat data. *International Journal of Remote Sensing*, 2(1), 71-82.
- Maritorena, S., Morel, A., and Gentili, B. (1994). Diffuse reflectance of oceanic shallow waters: Influence of water depth and bottom albedo. *Limnology and Oceanography*, 39(7), 1689-1703.
- Poursanidis, D., Traganos, D., Reinartz, P., and Chrysoulakis, N. (2019). On the use of Sentinel-2 for coastal habitat mapping and satellite-derived bathymetry estimation using downscaled coastal aerosol band. *International Journal of Applied Earth Observation and Geoinformation*, 80, 58-70.
- Stumpf, R. P., Holderied, K., and Sinclair, M. (2003). Determination of water depth with high-resolution satellite imagery over variable bottom types. *Limnology and Oceanography*, 48(1part2), 547-556.
