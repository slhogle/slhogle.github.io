---
layout: page
title: 
permalink: /TM_roseo_sar/data_analysis/correlation/
---

## Reference publication

__Hogle SL__, Thrash JC, Dupont CL, Barbeau KA. (2016). Trace metal acquisition by heterotrophic bacterioplankton with contrasting trophic strategies. Appl. Environ. Microbiol. 82(5): XX-XX [LINK](http://aem.asm.org/content/early/2016/01/04/AEM.03128-15)
___

## Introduction: Correlations between metal transporters and genome features

Here we are comuting pairwise correlations between the raw abundances of metal transporters observed in the genomes and their genome features/metadata obtained from IMG. We’ll calculate the correlations for a combined dataset of Roseobacter and SAR11 (N=64), but the paper also presents a correlation network of Roseobacter only. You should be able to subset the dataset yourself if you want to examine those correlation values. The visualizations in the paper were done in cytoscape, but the output from this analysis will be formatted for direct tabular import into cytoscape.

### Import the dataset



```R
metalCountData <- read.table("sar11_roseo_metaluptake_counts.tsv", sep="\t", row.names=1, header=TRUE)
genomeFeat <- read.table("sar11_roseo_genome_feat.tsv", sep="\t", row.names=1, header=TRUE)
genomeCategories <- read.table("sar11_roseo_categories.tsv", row.names=1, header=TRUE)
counts_feats <- cbind.data.frame(metalCountData, genomeFeat) # combine the genome features and transporter counts dataframes
counts_feats <- subset(counts_feats, select = -Biosynthetic_Count) # drop the Biosynthetic Count column
```

### Perform pairwise Spearman’ rank correlation on full dataset and writes table of results


```R
corr.counts_feats <- cor(counts_feats, method = "spearman")
write.table(corr.counts_feats, file = "correlationtable.tsv", sep = "\t", row.names = T)
```

### Calculate a p value for each pairwise correlation


```R
pvals <- array(0,c(dim(counts_feats)[2],dim(counts_feats)[2]))

for (i in 1:dim(counts_feats)[2]) for (j in 1:dim(counts_feats)[2]) 
  pvals[i,j] <- cor.test(counts_feats[,i],counts_feats[,j])$p.val 
```

### Corrects the p values for multiple comparisons and writes a table of the results


```R
rownames(pvals) <- colnames(counts_feats) # set up the data frame
colnames(pvals) <- colnames(counts_feats)
ltri <- lower.tri(pvals) 
utri <- upper.tri(pvals)
pvals[ltri] <- p.adjust(pvals[ltri], method = "fdr") 
pvals[utri] <- t(pvals)[utri]
write.table(pvals, file = "correlationtable_pvals.tsv", sep = "\t", row.names = T)
```

### Write a table formatted for import to cytoscape


```R
corr.edges <- NULL
for (i in 1:nrow(corr.counts_feats)) {
  for (j in 1:ncol(corr.counts_feats)) {
    corr.edges <- rbind(corr.edges, c(rownames(corr.counts_feats)[i], rownames(corr.counts_feats)[j], corr.counts_feats[i,j]))
  }
}

pval.edges <- NULL
for (i in 1:nrow(pvals)) {
  for (j in 1:ncol(pvals)) {
    pval.edges <- rbind(pval.edges, c(rownames(pvals)[i], rownames(pvals)[j], pvals[i,j]))
  }
}

to_cytoscape <- cbind.data.frame(corr.edges, pval.edges[,3])
colnames(to_cytoscape) <- c('node1', 'node2', 'rho', 'Pcorrected')
write.table(to_cytoscape, file = "cytoscape_import.tsv", sep = "\t", row.names = F)
```
