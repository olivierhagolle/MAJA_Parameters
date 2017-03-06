# MAJA_Parameters

This file provides a description of MAJA main parameters, which a user might want to change. We do not advise to change the other parameters.

# Miscellaneous parameters

```<!-- Maximum percentage of cloudy pixels for the product to be considered as valid (in percentage) -->
<Max_Cloud_Percentage>90</Max_Cloud_Percentage>```

If the cloud percentage within the image (No_data excluded), the output product is declared NotValid and not produced. We have seen that in general, with this percentage of clouds, the remaining pixels might be wrongly detected. If you want to remove this feature, just ser the parameter to 100%.


```<!-- Blue band code identifier -->
<Blue_Band_Code>B1</Blue_Band_Code>```

B2 might be used instead of B1. Anyway, we have obtained better results with B1. In case you want to change for B2, other settings will have to be changed, such as the <Blue_red_ratio> used in the multi spectral criterion to measure aerosols. 


## Masks

### Cirrus detection
```<!-- Offset for reflectance threshold to detect a cirrus -->
<Cirrus_Mask_Threshold_Offset>0.015</Cirrus_Mask_Threshold_Offset>
<!-- Gain for reflectance threshold to detect a cirrus -->
<Cirrus_Mask_Threshold_Gain>0.00002</Cirrus_Mask_Threshold_Gain>
```

We use Sentinel-2 B10 band to detect high clouds in Sentinel-2 imagery. A simple threshold would declare mountains as clouds as the can be seen above the water vapour layer, mainly stacked in the lower layers of the atmospherer.

As a result we implemented a linear model as a function f the altitude (in m)

**B10 Threshold(alt)= 0.015 + 0.00002*alt**

This criterion cannot detect the low clouds

### Snow detection
```<!--  Threshold on surface reflectance in the blue band -->
<NDSI_Threshold>0.4</NDSI_Threshold>
<!--  Threshold on the resd band surface reflectance absolute value -->
<Red_Reflectance_Threshold>0.12</Red_Reflectance_Threshold>
<!--  Threshold on the SWIR1 band surface reflectance absolute value -->
<SWIR1_Reflectance_Threshold>0.16</SWIR1_Reflectance_Threshold>
```

Pixels declared as snow must first be detected as clouds, then confirmed as snow. A snow pixel has a high reflectance in the visible, a lower reflectance in the SWIR, and a high Normalised Difference Snow Index.

### Mono-temporal threshold
```<!-- Threshold on surface reflectance in the blue band -->
 <Cloud_Blue_Reflectance_Threshold>0.22</Cloud_Blue_Reflectance_Threshold> ```
 
 Absolute and mono temporal threshold to detect clouds. Some very high desert sites might require to use a higher value. If working on a vegetated site, a lower value might be used.Anyway, towns are often bright. And the multi-temporal cloud mask is the main cloud detection method. 


```<!-- Threshold on surface reflectance in the Refl_water_band band for pixels declared water (if sunglint_flag is False) -->
<Water_Reflectance_Threshold>0.08</Water_Reflectance_Threshold>```


Corresponding threshold above water, in the SWIR (in absence of sunglint). As this is one of the only two criteria to detect clouds over water, this parameter is important.

# Multi-temporal threshold

```<!-- Minimum Threshold on surface reflectance variation with time in the blue band -->
<Min_Threshold_Var_Blue>0.025</Min_Threshold_Var_Blue>
<!-- Maximum Threshold on surface reflectance variation with time in the blue band -->
<Max_Threshold_Var_Blue>0.070</Max_Threshold_Var_Blue>```

This criterion is the main criterion of the cloud detection method. It has a minimum and maximum value. If the reference measurements was selected a few days ago, the threshold value is 0.025, if it was acquired more than two months ago, the threshold is 0.07


```<!-- Threshold of correlation between images of date D and D-1 -->
<Correl_Threshold>80</Correl_Threshold> ```
When a pixel is declared as a cloud but if its neighborhood correlates well with a previous image, then the pixel is removed from the cloud mask.