---
layout: page
title: 
permalink: /TM_roseo_sar/
---
![desk](/images/CCA_roseo_sar11.png)

__Supplemental Material and Datasets Reference:__
___
Hogle SL, Thrash JC, Dupont CL, Barbeau KA. _Accepted._ Trace metal acquisition by heterotrophic bacterioplankton with contrasting trophic strategies. _Appl. Environ. Microbiol._
___
<br>

You can download the datasets used in these analyses from [here](/files/roseo_sar11_datatables.tar.gz) or [here](http://dx.doi.org/10.6084/m9.figshare.1533034)

<iframe src="http://wl.figshare.com/articles/1533034/embed?show_title=1" width="568" height="485" frameborder="0"></iframe>

<br><br>
__Python implementation of the protein identification pipeline used in the paper__

* [1. Make rpsblast databases](/1_makeprofiledb)
* [2. rpsblast search - tab output](/2_rpsblast_tab)
* [3. Parse rpsblast tab output for top hits](/3_rpsblast_tab_tophits)
* [4. rpsblast top hits against NCBI CDD](/4_rpsblast_xml)
* [5. Parse xml rpsblast output to reduced form](/5_rpsbproc)
* [6. Parse and convert to wide tabular format](/6_rpsbproc_parse)

__Multivariate statistical analyses from the paper (in R)__

* [Fisher's exact test](/R_markdown/Hogle_etal_2015_fisher.html) for genome neighborhood enrichment
* [Ordination](/R_markdown/Hogle_etal_2015_ordination.html) of genomes based on transporter abundance and diversity
* [Correlation analysis](/R_markdown/Hogle_etal_2015_correlation.html) between transporters and genome features

