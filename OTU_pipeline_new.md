---
layout: page
title: 
permalink: /OTU_pipeline_new/
---

###Introduction
The OTU processing pipeline implemented here is basically the same as the ones recommended by Robert Edgar [here](http://drive5.com/usearch/manual/uparse_pipeline.html) and [here.](http://drive5.com/usearch/manual/upp_ill_pe.html) To run this pipeline you will need the [USEARCH](http://www.drive5.com/usearch/download.html) software which includes the UPARSE algorithm. This tutorial assumes a version of USEARCH of at least v8.1.1861

###Read quality control
Merge paired end reads, relabel each read as FILENAME.1,2,3...

```bash
usearch -fastq_mergepairs DNA07_S22_L001_R1_001.fastq \
-reverse DNA7_S22_L001_R2_001.fastq \
-relabel @ \
-fastqout merged.fq
```


Quality filter. Edgar says that for paired reads [length truncation is probably unnecessary.](http://drive5.com/usearch/manual/upp_readprep.html) Filter out reads with more than 1.0 total expected errors for all bases in the read. There is more information about the expected error prediction in USEARCH [here](http://drive5.com/usearch/manual/exp_errs.html) and [here.](http://bioinformatics.oxfordjournals.org/content/31/21/3476) This is more conservative threshold than in many other comparable OTU processing pipelines. Edgar recommends using the default threshold of 1.0. This command also relabels each sequence as Filt1, Filt2, Filt3...

```bash
usearch -fastq_filter DNA07_merged.fastq \
-fastq_maxee 1.0 \
-relabel Filt \
-fastaout filtered.fa
```

Concatenate all the sample files together into one big file

```bash
cat *.fa > allsamples.fa
```

###Read dereplication step

The input sequences to '-cluster\_otus' must be a set of unique sequences sorted in order of decreasing abundance with size annotations in the labels. The '-derep_fulllength' command finds the unique sequences and adds size annotaitions (ie roughly how many identical sequences there were for each uniq sequence).

```bash
usearch -derep_fulllength allsamples.fa \ 
-relabel Uniq \
-sizeout \ 
-fastaout allsamples_derep.fa
```

###OTU clustering step
Sorting and singleton filtering are combined into the OTU clustering step. Relabel each OTU as Otu1, Otu2, Otu3...

```bash
usearch -cluster_otus allsamples_derep.fa \
-minsize 2 \
-relabel Otu \
-otus allsamples_otus.fa
```

Additionally, in USEARCH v8.1.1861 there is an additional chimera filtering step included in the -cluster_otus command. Edgar states that it may be possible to find extra chimera sequences by running -uchime_ref to remove predicted chimeric sequences using a certified reference database. In this case we use the [UCHIME gold standard reference.](http://www.mothur.org/w/images/2/21/Greengenes.gold.alignment.zip) It seems that the extra chimera filtering step is mostly unecessary, so we won't be using it in this tutorial. Check the older pipeline for the commands for uchime.

###Taxonomy assignment

Interestingly, USEARCH v8.1.1861 contains a taxonomy assignment pipeline called [UTAX.](http://drive5.com/usearch/manual/utax_algo.html). Edgar states that "At a high level, UTAX is a k-mer based method which looks for words in common between the query sequence and reference sequences with known taxonomy. The main advantages of UTAX over previous classifiers such as the RDP Naive Bayesian Classifier (RDP) are very high speed, informative confidence values and flexible options for training on user-supplied data." The algorithm is currently not published, but it seems pretty robust from reading about it [here](http://drive5.com/usearch/manual/taxonomy_validation.html) and [here.](http://drive5.com/usearch/manual/tax_train.html)

In order to use the UTAX algorithm, you first must download the reference data for constructing the UTAX database from [here.](http://drive5.com/usearch/manual/utax_downloads.html) The datatbase is not precompiled or trained so you must do that yourself. 

Change directory into  '/rdp\_16s\_trainset15/fasta'

This command makes a UTAX formatted database from the RDP training set which is comprised of named isolate strains. For reasons not to use a full 16S database see [here.](http://drive5.com/usearch/manual/faq_utax_largedb.html)

```bash
usearch -makeudb_utax 16s_ref.fa \
-output 16s_ref.udb \
-report 16s_report.txt
```

This next command assigns taxonomy to each of the OTUs in the 'allsamples_otus.fa' file generated in the OTU clustering step.

```bash
usearch -utax allsamples_otus.fa \
-db 16s_ref.udb \
-strand both \
-fastaout allsamples_otus_tax.fa
```

###Assign reads to OTUs

Finally we can [map reads to OTUs](http://drive5.com/usearch/manual/mapreadstootus.html) and make an OTU table in a variety of formats. If OTUs have been assigned a taxonomy as in the step above this information is passed along into the OTU table. Here we generate output in a generic table format and the BIOM format which is compatible with QIIME.

```bash
usearch -usearch_global DNAO7/DNA07_merged.fq \
-db allsamples_otus_tax.fa \
-strand plus \
-id 0.97 \
-otutabout otutab.txt \
-biomout otutab.json
```

###Merge individual sample BIOM files into a single BIOM file
