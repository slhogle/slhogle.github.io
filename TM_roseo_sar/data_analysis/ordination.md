---
layout: page
title: 
permalink: /TM_roseo_sar/data_analysis/ordination/
---

## Reference publication

__Hogle SL__, Thrash JC, Dupont CL, Barbeau KA. (2016). Trace metal acquisition by heterotrophic bacterioplankton with contrasting trophic strategies. Appl. Environ. Microbiol. 82(5): XX-XX [LINK](http://aem.asm.org/content/early/2016/01/04/AEM.03128-15)
___

## Introduction: Ordination and multivariate statistics

Here we perform principle components analysis (PCA) and non-metric multidimmensional scaling (NMDS) in order to explore patterns of metal uptake genes in Roseobacter and SAR11. We also look at multivariate homogeneity of group dispersions for each taxonomic class in both NMDS and PCA ordinations in order to determine which group is more variable in terms of their transport capabilities.

### Load required libraries


```R
library("ggplot2")
library("vegan")
library("grid")
library("MASS")
```

### Import the dataset


```R
metalCountData <- read.table("sar11_roseo_metaluptake_counts.tsv", sep="\t", row.names=1, header=TRUE)
genomeFeat <- read.table("sar11_roseo_genome_feat.tsv", sep="\t", row.names=1, header=TRUE)
genomeCategories <- read.table("sar11_roseo_categories.tsv", row.names=1, header=TRUE)
```

### Create reduced dataframes

Dataframes without rows containing NA values in the Lifestyle category. Necessary for envfit analysis of factors


```R
metalCountData.reduc <- metalCountData[!is.na(genomeCategories$Lifestyle), ]
genomeCategories.reduc <- genomeCategories[!is.na(genomeCategories$Lifestyle), ]
```

### Vegan implementation of PCA


```R
metal.rda <- rda(metalCountData)
```

PCA on reduced dataset


```R
metal.rda.reduc <- rda(metalCountData.reduc)
```

### Fit environmental factors onto the reduced ordination

Using Lifestyle factor for environmnetal fitting


```R
set.seed(753)
ef.reduc <- envfit(metal.rda.reduc, genomeCategories.reduc, permu=999)
ef.reduc
```




    
    ***FACTORS:
    
    Centroids:
                               PC1     PC2
    LifestyleFreeliving    -0.5893  0.1681
    LifestyleSurface_assoc  0.6161 -0.1757
    TaxaRoseobacter         0.6262 -0.1673
    TaxaSAR11              -0.6547  0.1749
    
    Goodness of fit:
                  r2 Pr(>r)    
    Lifestyle 0.3284  0.001 ***
    Taxa      0.3674  0.001 ***
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    Permutation: free
    Number of permutations: 999
    




Here we see that both the classifications of taxa (Roseobacter vs SAR11) and the classification of Lifestyle (surface associated/patch adapted vs freeliving) are significantly associated with the ordination. So the differences in metal transporter repretoir appear to be related to the differences between taxa and Lifestyle.

### Multivariate homogeneity of group dispersions

Here we calculate dispersion values for euclidean distance based on SAR11 vs Roseobacter groups in order to determine which group has a larger dsipersion or spread



Need to do dispersion calculation on a distance matrix. Use euclidean because PCA uses euclidean distances


```R
metal_tran_dis <- vegdist(metalCountData, method = "euclidean")
disper <- betadisper(metal_tran_dis, genomeCategories$Taxa, bias.adjust=TRUE)
disper
```




    
    	Homogeneity of multivariate dispersions
    
    Call: betadisper(d = metal_tran_dis, group = genomeCategories$Taxa,
    bias.adjust = TRUE)
    
    No. of Positive Eigenvalues: 40
    No. of Negative Eigenvalues: 0
    
    Average distance to median:
    Roseobacter       SAR11 
          4.278       1.112 
    
    Eigenvalues for PCoA axes:
       PCoA1    PCoA2    PCoA3    PCoA4    PCoA5    PCoA6    PCoA7    PCoA8 
    737.4761 186.1731  91.6971  52.1355  35.0828  27.6643  24.7258  22.2809 




```R
permutest(disper, pairwise = TRUE)
```




    
    Permutation test for homogeneity of multivariate dispersions
    Permutation: free
    Number of permutations: 999
    
    Response: Distances
              Df Sum Sq Mean Sq      F N.Perm Pr(>F)    
    Groups     1 144.69 144.694 60.194    999  0.001 ***
    Residuals 62 149.04   2.404                         
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Pairwise comparisons:
    (Observed p-value below diagonal, permuted p-value above diagonal)
                Roseobacter SAR11
    Roseobacter             0.001
    SAR11        1.0391e-10      



We can see here that Roseobacter has a greater average distance to median (4.278) compared to SAR11 (1.112) and that this difference is significant based on permuted model residuals generating a permutations distribution of F under the null hypothesis that there is no difference in dispersion between groups. This indicates that Roseobacter trace metal transporter inventory is more diverse and variable than in SAR11.

### Plotting the PCA analysis

Uses scores function to put site PC values into scrcs. Then associate a variable with them - in this case using the Lifestyle category. The result is a dataframe


```R
scrs <- as.data.frame(scores(metal.rda, display = "sites"))
scrs <- cbind(scrs, lifestyle = genomeCategories$Lifestyle, taxa = genomeCategories$Taxa)
```

Fit factors and continuous variables to ordinations


```R
set.seed(152)
ef <- envfit(metal.rda, genomeFeat, permu = 999)
```

Use the score function again to load direction of vectors. Vectors here are weighted by R<sup>2</sup> value


```R
spp.scrs <- as.data.frame(scores(ef, display = "vectors"))
spp.scrs <- cbind(spp.scrs, Species = rownames(spp.scrs))
```

make the ggplot object


```R
ggplot(scrs) +
  geom_point(mapping = aes(x = PC1, y = PC2, shape = taxa, color = lifestyle), size = 4) + 
  theme_bw() +
  coord_fixed() + ## need aspect ratio of 1!
  geom_segment(data = spp.scrs, aes(x = 0, xend = PC1, 
                                    y = 0, yend = PC2), 
                                   arrow = arrow(length = unit(0.25, "cm")), 
                                   colour = "grey") +
  geom_text(data = spp.scrs, aes(x = PC1, y = PC2, label = Species), size = 3)
```


![svg](/TM_roseo_sar/data_analysis/output_33_0.svg)


### Vegan implementation of NMDS

We’ll now perform the same analysis as above using NMDS. NMDS is a popular and very robust ordination method, and it is frequently used in microbial ecology studies. We want to make sure that NMDS and PCA (both based on differing assumptions) don’t provide wildly different results.


```R
set.seed(471)
metal.mds <- metaMDS(metalCountData, trace = TRUE, plot = F, 
                     autotransform = FALSE, noshare = FALSE, 
                     wascores = FALSE, trymax = 1000)
```

    Run 0 stress 0.1042079 
    Run 1 stress 0.1041562 
    ... New best solution
    ... procrustes: rmse 0.008973122  max resid 0.04651044 
    Run 2 stress 0.1037959 
    ... New best solution
    ... procrustes: rmse 0.06409011  max resid 0.3321104 
    Run 3 stress 0.1052446 
    Run 4 stress 0.1062545 
    Run 5 stress 0.1062794 
    Run 6 stress 0.1015691 
    ... New best solution
    ... procrustes: rmse 0.05271375  max resid 0.1365035 
    Run 7 stress 0.1029558 
    Run 8 stress 0.1061895 
    Run 9 stress 0.1015154 
    ... New best solution
    ... procrustes: rmse 0.006515735  max resid 0.03648933 
    Run 10 stress 0.1067396 
    Run 11 stress 0.1015274 
    ... procrustes: rmse 0.001932787  max resid 0.006878349 
    *** Solution reached


### Multivariate homogeneity of group dispersions for NMDS

The same analysis as above but using Bray-Curtis distance. This is the default dissimilarity matrix used in the metaMDS function.

Need to do dispersion calculation on a distance matrix. Use bray distance


```R
metal_tran_dis <- vegdist(metalCountData, method = "bray")
disper <- betadisper(metal_tran_dis, genomeCategories$Taxa, bias.adjust=TRUE)
disper
```




    
    	Homogeneity of multivariate dispersions
    
    Call: betadisper(d = metal_tran_dis, group = genomeCategories$Taxa,
    bias.adjust = TRUE)
    
    No. of Positive Eigenvalues: 26
    No. of Negative Eigenvalues: 30
    
    Average distance to median:
    Roseobacter       SAR11 
         0.3628      0.3426 
    
    Eigenvalues for PCoA axes:
     PCoA1  PCoA2  PCoA3  PCoA4  PCoA5  PCoA6  PCoA7  PCoA8 
    7.4161 2.0080 1.4299 0.9981 0.8239 0.7594 0.6977 0.6206 




```R
permutest(disper, pairwise = TRUE)
```




    
    Permutation test for homogeneity of multivariate dispersions
    Permutation: free
    Number of permutations: 999
    
    Response: Distances
              Df Sum Sq   Mean Sq      F N.Perm Pr(>F)
    Groups     1 0.0059 0.0058997 0.2734    999  0.611
    Residuals 62 1.3378 0.0215781                     
    
    Pairwise comparisons:
    (Observed p-value below diagonal, permuted p-value above diagonal)
                Roseobacter SAR11
    Roseobacter             0.602
    SAR11           0.60292      



So it looks like there is no significant difference in group dispersion between Roseobacter or SAR11 in the NMDS/Bray-Curtis case. Why is this result different than that for PCA which relies on a Euclidean dissimilarity structure? Let’s plot the NMDS plot to look closer…


```R
plot(metal.mds, type = "p")
text(metal.mds, display = "sites")
```

    Warning message:
    In ordiplot(x, choices = choices, type = type, display = display, : Species scores not available


![svg](/TM_roseo_sar/data_analysis/output_43_1.svg)


In the NMDS ordination it looks like both AAA240-E13 and HIMB058 are big outliers. These genomes are not complete and/or amplified by MDA so they might not be refelecting a real trend here (potentially missing genes related to metal transport). Since NMDS is based on rank rather than absolute distances you lose information regarding the large differences in magnitude that you would expect with an analysis like PCA between SAR11 and Roseobacter. For example, instead of HTCC1002 being X arbitrary units away from Ruegeria TrichCH4B, HTCC1002 would be the “first, second, or third and so on” most distant object from CH4B. In that respect, in NMDS a lot of the biggest differences in magnitude between positioning in the ordination get compressed while smaller changes tend to be amplified.

Lets look at this again but omit AAA240-E13 and HIMB058 since they may be valid outliers.


```R
set.seed(471)
metalCountData.NOSAG <- metalCountData[-c(42,43), ]     #remove AAA240-E13 and HIMB058
genomeCategories.NOSAG <- genomeCategories[-c(42,43), ] #remove AAA240-E13 and HIMB058
genomeFeat.NOSAG <- genomeFeat[-c(42,43), ]             #remove AAA240-E13 and HIMB058
```


```R
metal.mds.NOSAG <- metaMDS(metalCountData.NOSAG, trace = TRUE, 
                           plot = F, autotransform = FALSE, 
                           noshare = FALSE, wascores = FALSE, 
                           trymax = 1000)
```

    Run 0 stress 0.1191187 
    Run 1 stress 0.1336855 
    Run 2 stress 0.1191504 
    ... procrustes: rmse 0.003718555  max resid 0.01963279 
    Run 3 stress 0.123536 
    Run 4 stress 0.1191234 
    ... procrustes: rmse 0.003292331  max resid 0.01538293 
    Run 5 stress 0.123536 
    Run 6 stress 0.1226624 
    Run 7 stress 0.1190997 
    ... New best solution
    ... procrustes: rmse 0.003516377  max resid 0.02218278 
    Run 8 stress 0.1226623 
    Run 9 stress 0.1226624 
    Run 10 stress 0.1235359 
    Run 11 stress 0.1235359 
    Run 12 stress 0.123536 
    Run 13 stress 0.1235359 
    Run 14 stress 0.1235359 
    Run 15 stress 0.1375248 
    Run 16 stress 0.1191233 
    ... procrustes: rmse 0.005829302  max resid 0.03454454 
    Run 17 stress 0.1339936 
    Run 18 stress 0.1191471 
    ... procrustes: rmse 0.00501204  max resid 0.03359612 
    Run 19 stress 0.1190998 
    ... procrustes: rmse 7.917323e-05  max resid 0.0003379384 
    *** Solution reached


Now we’ll look again at the group dispersions. Need to do dispersion calculation on a distance matrix. Use bray distance


```R
metal_tran_dis.NOSAG <- vegdist(metalCountData.NOSAG, method = "bray")
disper.NOSAG <- betadisper(metal_tran_dis.NOSAG, genomeCategories.NOSAG$Taxa, bias.adjust=TRUE)
disper.NOSAG
```




    
    	Homogeneity of multivariate dispersions
    
    Call: betadisper(d = metal_tran_dis.NOSAG, group =
    genomeCategories.NOSAG$Taxa, bias.adjust = TRUE)
    
    No. of Positive Eigenvalues: 25
    No. of Negative Eigenvalues: 29
    
    Average distance to median:
    Roseobacter       SAR11 
         0.3628      0.2856 
    
    Eigenvalues for PCoA axes:
     PCoA1  PCoA2  PCoA3  PCoA4  PCoA5  PCoA6  PCoA7  PCoA8 
    7.4055 1.9920 1.1716 0.9338 0.7788 0.7025 0.6375 0.5098 




```R
permutest(disper.NOSAG, pairwise = TRUE)
```




    
    Permutation test for homogeneity of multivariate dispersions
    Permutation: free
    Number of permutations: 999
    
    Response: Distances
              Df  Sum Sq  Mean Sq      F N.Perm Pr(>F)   
    Groups     1 0.08071 0.080712 9.5915    999  0.005 **
    Residuals 60 0.50490 0.008415                        
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Pairwise comparisons:
    (Observed p-value below diagonal, permuted p-value above diagonal)
                Roseobacter SAR11
    Roseobacter             0.002
    SAR11         0.0029714      



Now these dispersion differences appear to be significant - albeit they are smaller in magnitude than what we saw for the euclidean distance in the PCA analysis. Still it is good to see the two methods generally agreeing, even though their underlying assumptions are quite different. Let’s now plot up the NMDS ordination missing the potentially incomplete genomes

Uses scores function to put site PC values into scrcs. Then associate a variable with them - in this case using the Lifestyle category. The result is a dataframe


```R
scrs.mds <- as.data.frame(scores(metal.mds.NOSAG, display = "sites"))
scrs.mds <- cbind(scrs.mds, lifestyle = genomeCategories.NOSAG$Lifestyle, taxa = genomeCategories.NOSAG$Taxa)
```

Fit factors and continuous variables to ordinations


```R
set.seed(152)
ef.mds <- envfit(metal.mds.NOSAG, genomeFeat.NOSAG, permu = 999)
```

Use the score function again to load direction of vectors. Vectors here are weighted by R<sup>2</sup> value


```R
spp.scrs.mds <- as.data.frame(scores(ef.mds, display = "vectors"))
spp.scrs.mds <- cbind(spp.scrs.mds, Species = rownames(spp.scrs.mds))
```

make the ggplot object


```R
ggplot(scrs.mds) +
  geom_point(mapping = aes(x = NMDS1, y = NMDS2, shape = taxa, color = lifestyle), size = 4) + 
  theme_bw() +
  coord_fixed() + ## need aspect ratio of 1!
  geom_segment(data = spp.scrs.mds, aes(x = 0, xend = NMDS1, 
                                    y = 0, yend = NMDS2), 
                                   arrow = arrow(length = unit(0.25, "cm")), 
                                   colour = "grey") +
  geom_text(data = spp.scrs.mds, aes(x = NMDS1, y = NMDS2, label = Species), size = 3)
```


![svg](/TM_roseo_sar/data_analysis/output_58_0.svg)


## Conclusion

It appears that PCA seems to be more sensitive to the “double zero problem” which is that ecological sites will tend to cluster based on species with many shared absences as opposed to those species with shared presences (paraphrased from Numerical Ecology by Legendre and Legendre). This is cited as a problem by Legendre and Legendre because traditional ecological studies treat double zeros aka shared absences of species as uninformative presumably due to the risk of false negatives. However, in our case zero values are true values. The absence of a gene in a set of genomes is actually informative in our case given that our risk for false negatives is effectively zero (albeit except for cases of the SAG and mda genomes). Therefore, we think that euclidean distance PCA best represents our data here. We do need to be careful about genome completeness, but at this stage in our exploratory analysis we think that it is unnecessary to omit AAA240-E13 and HIMB058 as they represent valuable phylogenetic diversity not encompassed by the other strains.
