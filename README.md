# MAJA_Parameters

This file provides a description of MAJA main parameters, which a user might want to change. We do not advise to change the other parameters.

<!-- Maximum percentage of cloudy pixels for the product to be considered as valid (in percentage) -->`
<Max_Cloud_Percentage>90</Max_Cloud_Percentage>`

If the cloud percentage within the image (No_data excluded), the output product is declared NotValid and not produced. We have seen that in general, with this percentage of clouds, the remaining pixels might be wrongly detected. If you want to remove this feature, just ser the parameter to 100%.


`<!-- Blue band code identifier -->
<Blue_Band_Code>B1</Blue_Band_Code>    `

B2 might be used instead of B1. Anyway, we have obtained better results with B1. In case you want to change for B2, other settings will have to be changed, such as the <Blue_red_ratio> used in the multi spectral criterion to measure aerosols. 


`<!-- Threshold on surface reflectance in the blue band -->
 <Cloud_Blue_Reflectance_Threshold>0.22</Cloud_Blue_Reflectance_Threshold>   `
 
 Absolute and mono temporal threshold to detect clouds. Some very high desert sites might require to use a higher value. If working on a vegetated site, a lower value might be used.Anyway, towns are often bright. And the multi-temporal cloud mask is the main cloud detection method. 
