---
layout: page
title: 
permalink: /TM_roseo_sar/mainpage/
---
![desk](/TM_roseo_sar/CCA_roseo_sar11.png)



## Reference publication

__Hogle SL__, Thrash JC, Dupont CL, Barbeau KA. (2016). Trace metal acquisition by heterotrophic bacterioplankton with contrasting trophic strategies. _Appl. Environ. Microbiol._ 82(5): XX-XX [LINK](http://aem.asm.org/content/early/2016/01/04/AEM.03128-15)
___

You can download the datasets used in these analyses from [here](http://dx.doi.org/10.6084/m9.figshare.1533034) or [here](/TM_roseo_sar/1533034.zip).
 
<iframe src="https://widgets.figshare.com/articles/1533034/embed?show_title=0" width="568" height="351" frameborder="0"></iframe>

__Python implementation of the protein identification pipeline used in the paper__

* [1. Make rpsblast databases](/TM_roseo_sar/annotation_pipeline/1_makeprofiledb)
* [2. rpsblast search - tab output](/TM_roseo_sar/annotation_pipeline/2_rpsblast_tab)
* [3. Parse rpsblast tab output for top hits](/TM_roseo_sar/annotation_pipeline/3_rpsblast_tab_tophits)
* [4. rpsblast top hits against NCBI CDD](/TM_roseo_sar/annotation_pipeline/4_rpsblast_xml)
* [5. Parse xml rpsblast output to reduced form](/TM_roseo_sar/annotation_pipeline/5_rpsbproc)
* [6. Parse and convert to wide tabular format](/TM_roseo_sar/annotation_pipeline/6_rpsbproc_parse)

__Multivariate statistical analyses from the paper (in R)__

* [Fisher's exact test](/TM_roseo_sar/data_analysis/fisher) for genome neighborhood enrichment
* [Ordination](/TM_roseo_sar/data_analysis/ordination) of genomes based on transporter abundance and diversity
* [Correlation analysis](/TM_roseo_sar/data_analysis/correlation) between transporters and genome features

