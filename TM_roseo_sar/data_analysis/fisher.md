---
layout: page
title: 
permalink: /TM_roseo_sar/data_analysis/fisher/
---

## Reference publication

__Hogle SL__, Thrash JC, Dupont CL, Barbeau KA. (2016). Trace metal acquisition by heterotrophic bacterioplankton with contrasting trophic strategies. Appl. Environ. Microbiol. 82(5): XX-XX [LINK](http://aem.asm.org/content/early/2016/01/04/AEM.03128-15)
___

## Introduction: Fisher’s exact test

Here we test using Fisher’s exact test to test whether certain protein domains/families are more likely to be found inside the genomic neighborhood (10 genes upstream and downstream) of MCL clusteres than outside of them.

### Import the dataset


```R
fisher.tables <- read.table("fishertest.tsv", sep="\t", header=TRUE)
fisher.tables.red <- as.matrix(subset(fisher.tables, 
                                      select = c("target_in_neighborhood", 
                                                 "others_in_neighborhood", 
                                                 "target_out_neighborhood", 
                                                 "others_out_neighborhood")))
```

### Perform Fisher’s exact test

Here the p values from the fisher.test function are read into a vector “p1.” The odds ratio values for those tests are read into a different vector called “or.”


```R
p1 <- apply(fisher.tables.red, 1, function(x) fisher.test(matrix(x,nr=2))$p.value)
or <- apply(fisher.tables.red, 1, function(x) fisher.test(matrix(x,nr=2))$estimate)
```

### Adjust p values using the False Discovery Rate


```R
p1.adjust <- p.adjust(p1, method = "fdr", n = length(p1))
```

### Format table and write to output


```R
output <- cbind.data.frame(fisher.tables$TBDTcluster, fisher.tables$proteinFamily, p1.adjust, or)
colnames(output) <- c("TBDT_MCL_cluster", "proteinDomain", "Pvalue", "OddsRatio")
write.table(output, file = "fishertestoutput.tsv", sep = "\t", row.names = F)
```
