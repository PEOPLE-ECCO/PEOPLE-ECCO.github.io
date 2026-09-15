# Getting Started with Habitat Extent 

This page provides a practical quick start for running the Habitat Extent product. This workflow also automatically creates the habitat connectivity product, based on the results of the extent product.

## What the Habitat Extent product does 

The Habitat Extent product generates maps of aquatic habitat types, submerged aquatic vegetation (SAV) and coral reefs, for a defined area and time period. A general description of the workflow is given below, with more detailed explanations on each step listed in the following sections: 

- All available Sentinel-2 Level-2A surface reflectance imagery below a user-defined scene cloud cover percentage are downloaded from [Openeo](!https://openeo.org/), based on the time period chosen. 

- Cloud and pixel quality filters are applied using several bands and meta-data of each image.  

- Several transformations are applied to each image, creating the necessary features used by a Random Forest classifier.

- The model is used to predict habitat classes on each downloaded image, and return the different class probabilities on a pixel basis for each image of the time-series. 

- The main result over the time period is created by computing the average probability for each class over all the predicted images. This step is necessary to create a stable and reliable result, to lower the impact of metereological conditions.


The main outputs are: 

- Habitat type maps (e.g. SAV, coral reef).

- Probability maps for different habitat types.

- Binary maps indicating the presence of SAV or coral.

- Vector files delimiting the area for all detected habitat patches.

## Creation of a new habitat extent run

### Step 1 Area selection

After logging into the portal select the __region of interest__ in which you want to use the model, within the available __sites__. 

![Step 1](../../../asset/sav_start_page.png)


### Step 2 Create a new time-series

Click on the `+` symbol at the bottom of the existing time-series, indicated by a red arrow in the figure below. You might need to scroll down the __Timeseries View __ menu to see this button.

![Step 2](../../../asset/sav_habitat_new_ts.png)

### Step 3 Select A time range

From there on, follow the steps indicated by the pop-up window for creating a timeseries. After selecting the __algorithm__, you will need to select a __start and an end date__ to run the habitat extent product.

![Step 3](../../../asset/sav_habitat_timewindow.png)


### Step 4 Select an Area Of Interest (AOI)

You can __create the polygon delimiting your area of interest__ by clicking on the map. To finish the polygon, you simply need to left click on the first point of your polygon.

![Step 4](../../../asset/sav_habitat_aoi_selection.png)


### Step 5 Start the run!

Check the __parameters__ of your run, and click on the `Create` button to start the computations. Depending on the scale of the area and the length of the timeseries, the process might take from 5 minutes to several hours.

![Step 5](../../../asset/sav_habitat_create.png)






## Suggested first run 

- Start with a single AOI, the relevant model and limited time period.

- Preview available Sentinel 2 imagery on the [Copernicus Browser](!https://browser.dataspace.copernicus.eu/), and select a time period with clear water and atmospheric conditions. It is also possible on this viewer to change the cloud cover percentage to preview which scenes would be available at different values.

- If there is a single clear image, you can select a very short time window including only this scene. This type of scene generally yields good prediction results, and is fast to run.

- Inspect outputs for:  

    - Misclassification in turbid or deep water, both for the entire time series, and on per-scene basis.

    - Edge artefacts along coastlines and clouds.

- Adjust the model parameters if needed.
