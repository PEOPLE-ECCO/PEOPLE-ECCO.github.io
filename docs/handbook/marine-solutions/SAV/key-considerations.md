# Habitat Extent: Key Considerations

## Environmental detectability

Submerged habitat mapping depends strongly on turbidity, suspended sediment, water depth, light attenuation, sun glint, surface state, and seasonal water quality. Select periods with stable clarity, little surface disturbance, and low cloud cover.

## Temporal selection

Seasonal or annual composites balance accuracy and processing efficiency. The selected period affects habitat visibility, classification consistency, and sensitivity to seasonal variation. Avoid combining observations from strongly contrasting periods.

## Bioregion representativeness

Model performance is highest where ecological and optical conditions resemble those represented in training. Select the correct bioregion model and use caution in transitional or mixed coastal environments.

## Input quality

Cloud and land masking use SWIR and cloud-probability filters, but thin cloud, haze, glint, turbidity, and limited valid observations may remain. Inspect the composite and review outputs in data-poor areas.

## Post-processing

Sieve filtering improves coherence but may remove or generalize small and fragmented patches. This matters particularly in fragmented coastal systems and small restoration sites.



## Compositing 

- Composition strategy  

    - Aggregation of predictions mad eon every scene 


## Feature extraction 

The classification uses a fixed set of derived features: 

- Spectral indices, including: 

    - RDVI 

    - Normalized Blue-Red index 

    - Normalized Blue-Green index 

    - Normalized NIR-Blue index 

    - RVI 

    - Red-Blue ratio 

    - Blue-Green ratio 

    - NIR-Blue ratio 

    - NIR-Red ratio 

- Local mean using gaussian smoothing

- Spatial statistics comparing each pixel to its surroundings

- Structural (texture) features derived using multi-scale feature extraction 

### Classification 

- Model type 

    - Random Forest classifier (pre-trained for the selected bioregion)  

- The model: 

    - Uses spectral, spatial, and structural features 

    - Produces pixel-wise habitat classification 

### Post-processing 

- Probability thresholding
- Sieve filtering, removes isolated pixels and improves spatial coherence 