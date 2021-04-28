# MAJA_Parameters

The parameters of MAJA have been tuned to work on all sorts of sites, from the agricultural regions to the tropical forests, and from the deserts to the snow covered regions. A local adaptation of the parameters could improve the results slightly. This document provides a description of MAJA main parameters, which a user might want to change. We do not advise to change the other parameters, which are in the parameter files just because it is a bad practice to hard code parameters. 

MAJA has two types of parameters, the GIPP (the processing parameters) and the LUTS (LUTS). The MAJA repository explains how to retrieve them (it might change with time so I do not want to introduce too many dependencies). The main parameters are concentrated in the L2COMM files (L2A common parameters). 

Here is an example of such L2COMM files, applicable to Sentinel-2A, that involves the access to Copernicus atmosphere (CAMS) forecasts to constrain the aerosol type in the retrievals :
http://osr-cesbio.ups-tlse.fr/gitlab_cesbio/kettigp/maja-gipp/blob/develop/SENTINEL2_TM_CAMS/S2A_TEST_GIP_L2COMM_L_ALLSITES_00001_20190626_21000101.EEF

Most of the information is duplicated for similar sensors (S2A and S2B). Moreover, for the "TM" format, and for a bad reason that still applies, the L2COMM is also necessary twice, with a  very small difference. Make sure you change both files (we should remove that one day). 

# Miscellaneous parameters

` <!-- Maximum percentage of cloudy pixels for the product to be considered as valid (in percentage) -->
<Max_Cloud_Percentage>90</Max_Cloud_Percentage> `

If the cloud percentage within the image (No_data excluded) is above that threshold, the output product is declared NotValid and not produced. We have seen that in general, with this percentage of clouds, the remaining pixels might be wrongly detected. If you want to remove this feature, just ser the parameter to 100%.


`<!-- Blue band code identifier --> <Blue_Band_Code>B1</Blue_Band_Code>`

B2 might be used instead of B1. Anyway, we have obtained better results with B1. In case you want to change for B2, other settings will have to be changed, such as the <Blue_red_ratio> used in the multi spectral criterion to measure aerosols. 


## Masks

### Cirrus detection
```
<Cirrus_Mask_Threshold_Offset>0.007</Cirrus_Mask_Threshold_Offset>
      <!-- Gain for reflectance threshold to detect a cirrus -->
<Cirrus_Mask_Threshold_Gain>0.000012</Cirrus_Mask_Threshold_Gain>
      <!-- Cirrus altitude reference - Used in ReflectanceThresholdImage -->

```

We use Sentinel-2 B10 band to detect high clouds in Sentinel-2 imagery. A simple threshold would declare mountains as clouds as the can be seen above the water vapour layer, mainly stacked in the lower layers of the atmospherer. As a result we implemented a quadratic model as a function of the altitude (in m)

**B10 Threshold(alt)= 0.007 + 0.000012*alt²**

This criterion cannot detect the low clouds. Reducing the offset value would detect more clouds, but a large part of these clouds are transparent enough to be used in users applications.

### Snow detection
```
<!--  Threshold on surface reflectance in the blue band -->
<NDSI_Threshold>0.4</NDSI_Threshold>
<!--  Threshold on the resd band surface reflectance absolute value -->
<Red_Reflectance_Threshold>0.12</Red_Reflectance_Threshold>
<!--  Threshold on the SWIR1 band surface reflectance absolute value -->
<SWIR1_Reflectance_Threshold>0.16</SWIR1_Reflectance_Threshold>
```

Pixels declared as snow must first be detected as clouds, then confirmed as snow. A snow pixel has a high reflectance in the visible, a lower reflectance in the SWIR, and a high Normalised Difference Snow Index. It is quite common that highly turbid waters are classified as snow. As MAJA is dedicated to lands, we have not tried to correct this defect.

### Mono-temporal threshold

#### Above lands
```
<!-- Threshold on surface reflectance in the blue band -->
 <Cloud_Blue_Reflectance_Threshold>0.24</Cloud_Blue_Reflectance_Threshold>
``` 
Absolute and mono temporal threshold to detect clouds. Some very high desert sites might require to use a higher value. If working on a vegetated site, a lower value might be used, but some bright buildings might be classified as clouds. As most clouds are detected using the multi-temporal method, we did not take the risk to discard pixels with this threshold, which is above all useful for the initialisation of MAJA.

#### Above water
```
<!-- Threshold on surface reflectance in the Refl_water_band band for pixels declared water (if sunglint_flag is False) -->
<Water_Reflectance_Threshold>0.04</Water_Reflectance_Threshold>
<Water_Sunglint_Reflectance_Threshold>0.16</Water_Sunglint_Reflectance_Threshold>
      <!-- Minimum Threshold on surface reflectance variation with time in the blue band -->
```
Above water, the multi-temporal criterai don't work well because the reflectances are subject to large changes. We use a threshold in the SWIR, when available in the sensor, or NIR otherwise. However, when the geometry of acquisition is close to specular conditions, another threshold applies. The intensity of sunglint is highly variable, depending on water surface roughness, and our cloud masking close to sunglint conditions is not very accurate. 

### Multi-temporal threshold
For each pixel, the multi-temporal criterion compares the current image to a reference mosaic of the most recent cloud free pixel. The acquisition date of each pixel depends on its cloud cover history.  The greater the time lag between the current image and the reference, the more chances the surface reflectance has evolved. As a result, the multi-temporal threshold depends on the time lag. 

```
      <!-- Minimum Threshold on surface reflectance variation with time in the blue band -->
      <Min_Threshold_Var_Blue>0.016</Min_Threshold_Var_Blue>
      <!-- Maximum Threshold on surface reflectance variation with time in the blue band -->
      <Max_Threshold_Var_Blue>0.060</Max_Threshold_Var_Blue>
      <!-- Time delay after which Refl_Variation_Threshold is doubled (in days) -->
      <Cloud_Forgetting_Duration>45</Cloud_Forgetting_Duration>
```

This criterion is the main criterion of the cloud detection method. It has a minimum and maximum value. We have defined another parameter, that we call "forgetting duration", which sets the duration of gap after which the multi-temporal threshold doubles. In places where vegetation changes are slow, such as deserts or tropical forests, the forgetting duration could be increased to 60 for instance. The minimum threshold Value is tuned when the cloud detection is performed at 240m. Version 4 of MAJA allows for a processing of cloud detection at 120 m resolution, and in that case, a value of 0.018 seems preferable.

Multi_temporal_threshold = max( Max_Threshold_Var_Blue, Min_Threshold_Var_Blue (1+ lag/Cloud_Forgetting_Duration))

```
<!-- Threshold of correlation between images of date D and D-1 -->
<Correl_Threshold>80</Correl_Threshold> 
```
When a pixel is declared as a cloud but its neighborhood correlates well with a previous image, it indicates that the surface is clearly visible.



### Shadow detection
The shadow detection method is complex and adaptative to the images, we do not recomment to change its parameters

