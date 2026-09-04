# Getting Started with Habitat Connectivity

This page provides a practical quick start for running the Habitat Connectivity product and understanding how spatial habitat structure and connectivity are derived from classified habitat maps.

## What the Habitat Connectivity product does

The Habitat Connectivity product analyzes the spatial structure and arrangement of habitats to quantify how habitat patches are distributed and connected across the landscape.

The product:

- Uses classified habitat maps, typically from the Habitat Extent product
- Identifies discrete habitat patches
- Computes spatial metrics describing patch size and connectivity between habitat areas

Main outputs include patch size and distribution maps. These outputs help distinguish large continuous habitats, fragmented or isolated patches, and areas with high or low structural connectivity.

## Select the input dataset

Before running the product, ensure that the input habitat maps are suitable for spatial analysis.

### Input requirements

- A classified habitat map for a single year or selected representative period
- Consistent spatial resolution, typically 10 m
- Binary or simplified habitat classes, such as habitat versus non-habitat

### Recommended input choice

Use either:

- A single-year habitat map for structural analysis
- A filtered or persistent habitat layer from the Habitat Frequency product

Persistent habitat, for example at least 70% frequency, is often recommended so that connectivity reflects stable ecological structures rather than temporary or noisy patterns.

## Key runtime parameters

### Minimum required inputs

- `spatial_extent`: Area of interest
- `habitat_raster`: Input habitat classification, binary or categorical

### Patch definition parameters

- `minimum_patch_size`: Minimum patch size included in the analysis, defined as the number of pixels per patch. The current default is 1,000 pixels. Smaller patches are excluded to reduce noise and insignificant fragments.
- `connectivity_distance_threshold`: Maximum distance at which two patches are considered connected. The current default is 1,000 m. Connectivity is distance-based rather than based on 4- or 8-neighbour pixel adjacency. This parameter should be user-defined in future versions.

### Connectivity analysis parameters

- `connectivity_metric`: Defines how connectivity between habitat patches is assessed. The current implementation focuses on patch-level relationships determined using the distance threshold.

## What gets written

Each run produces raster outputs and derived metrics describing habitat structure.

### Main outputs

- **Patch map**: Identified habitat patches
- **Patch size layer**: Area of each detected patch

## Suggested first run

- Start with a clean and validated habitat map and a limited AOI
- Use the default patch-size threshold and standard connectivity metric
- Inspect patch boundaries and fragmentation patterns
- Verify that continuous habitat is represented as continuous patches and that small artefacts are not retained
- Adjust minimum patch size or preprocessing if needed

## Tutorial outline

1. Open the Habitat Connectivity workflow.
2. Select a habitat map or filtered habitat layer.
3. Set the neighbouring distance and select the connectivity metric.
4. Launch and monitor the workflow.
5. Review connectivity maps and export outputs for GIS use.
