# Getting Started with Habitat Frequency

## What the product does

The Habitat Frequency product quantifies temporal habitat persistence from a time series of habitat extent maps.

The workflow:

- Uses multi-year habitat classifications
- Builds a consistent temporal stack
- Calculates the proportion of observations in which each pixel is classified as habitat

Outputs can include 0 to 100% frequency maps, thresholded persistent-habitat layers, and indicators of stable versus variable habitat presence.

## Input dataset and period

### Input requirements

- A multi-year series of habitat maps, typically annual
- Consistent extent, resolution, and classification workflow
- Comparable seasonal timing across years

A minimum of roughly four to five years is recommended in the source documentation.

## Key parameters

- `spatial_extent`: Area of interest
- `habitat_timeseries`: Multi-year set of habitat classifications
- `analysis_period`: Years included
- `frequency_threshold`: Threshold defining persistent habitat, for example 70% for highly stable habitat or 50% for moderately stable habitat
- `temporal_consistency_check`: Optional alignment check
- `data_gaps_handling`: Treatment of missing years

## Outputs

- Continuous habitat-frequency raster from 0 to 100%
- Optional binary persistence layer
- Optional summary statistics for persistent and variable habitat area

## Suggested first run

- Start with validated habitat-extent maps and a small AOI
- Use the full available period and a default threshold such as 70%
- Inspect expected stable and variable areas
- Compare with local knowledge or reference data
- Adjust the period or threshold if needed

## Tutorial outline

1. Open the Habitat Frequency workflow.
2. Select the multi-year habitat extent maps.
3. Define the period and frequency threshold.
4. Launch and monitor processing.
5. Inspect and export frequency outputs.
