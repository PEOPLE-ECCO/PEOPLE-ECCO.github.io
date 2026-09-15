# Habitat Frequency: Theoretical Background

## Temporal dynamics

Aquatic habitats such as submerged aquatic vegetation and coral reefs vary through seasonal growth, changes in water quality and light, storms, eutrophication, and human pressures. Multi-year analysis helps distinguish stable ecosystems from regions undergoing degradation or recovery.

## Persistence and frequency

Persistence describes the ability of a habitat to remain present over time. Spatially, it can be expressed as the proportion of observations in which habitat is present at a pixel, commonly reported from 0 to 100%.

Frequency supports identification of stable core habitats, variable or transient areas, and differences between regions.

## Time-series remote sensing

Repeated Earth Observation captures gradual trends, abrupt change, variability, and stability. Sentinel-2 provides a long archive, frequent observations, and consistent spatial resolution, enabling the transition from single-date maps to multi-temporal monitoring.

## Temporal aggregation

The product combines habitat classifications from multiple years and computes per-pixel occurrence frequency. Aggregation can reduce noise and highlight persistent spatial patterns, but it discards the exact timing and sequence of changes.

## Observation conditions

Optical detectability also depends on water-column properties, turbidity, suspended matter, and atmospheric effects. Apparent absence may therefore indicate poor observation conditions rather than true habitat absence.

## Uncertainty propagation

Frequency values inherit classification errors, spectral confusion, and noise from annual maps. Aggregation can accumulate these effects and bias areas with marginal detectability.

## Monitoring applications

Frequency layers can identify stable and resilient ecosystems, potential decline or recovery, transition zones, and candidate areas for conservation, restoration, or targeted management.

## Adopted approach

The workflow uses consistent multi-year habitat maps, temporal stacking, per-pixel frequency calculation, and persistence thresholds. It balances interpretability, robustness at scale, and operational usability.
