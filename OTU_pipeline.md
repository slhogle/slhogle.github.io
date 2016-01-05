---
layout: page
title: 
permalink: /OTU_pipeline/
---

###Introduction
The OTU processing pipeline implemented here is basically the same as the ones recommended by Robert Edgar [here](http://drive5.com/usearch/manual/uparse_pipeline.html) and [here.](http://drive5.com/usearch/manual/upp_ill_pe.html) The only difference is that it doesn't use USEARCH to assign taxonomy. To run this pipeline you will need the [USEARCH](http://www.drive5.com/usearch/download.html) software which includes the UPARSE algorithm.

[Here](http://drive5.com/python/) is a collection of Python scripts useful for working with USEARCH

###Read quality control
Merge paired end reads, allowing no differences in the overlap region.

```bash
usearch -fastq_mergepairs DNA8_S22_L001_R1_001.fastq \
-reverse DNA8_S22_L001_R2_001.fastq \
-fastq_maxdiffs 0
-fastqout DNA7_merged.fastq
```

Get stats on the quality of the reads

```bash
usearch -fastq_stats DNA07_merged.fastq -log stats.log
```

Quality filter. Truncate reads at 250 bp and at a quality of 15.

```bash
usearch -fastq_filter DNA07_merged.fastq \
-fastq_trunclen 250 \
-fastq_truncqual 15 \
-fastaout DNA7_merged.filtered.fasta 
```

sed command to add barcodelabel tag for downstream demultiplexing. USEARCH cannot parse whitespace

```bash
sed 's/^>\(.*\)/>\1;barcodelabel=DNA8/' \
DNA8_filtered.fa | sed 's/\s//' > DNA8.fa
```

Concatenate all the sample files together into one big file

```bash
cat *.fa > allsamples.fa
```

###Read dereplication step

The input sequences to '-cluster\_otus' must be a set of unique sequences sorted in order of decreasing abundance with size annotations in the labels. The '-derep_fulllength' command finds the unique sequences and add the size annotaitions.

```bash
usearch -derep_fulllength allsamples.fa \ 
-relabel Uniq \
-sizeout \  
-fastaout allsamples_derep.fa
```

###OTU clustering step
This command will sort the OTUs by size cluster size and discard singleton sequences

```bash
usearch -sortbysize allsamples_derep.fa \
-minsize 2 \
-output allsamples_sorted.fa 
```

This command will cluster the sequences into OTUs

```bash
usearch -cluster_otus allsamples_sorted.fa \
-otus allsamples_otus.fa
```

Now we can remove predicted chimeric sequences using a certified reference database. In this case we use the [UCHIME gold standard reference.](http://www.mothur.org/w/images/2/21/Greengenes.gold.alignment.zip) In the [UPARSE tutorial](http://drive5.com/usearch/manual/upp_ill_pe.html) this is ommitted. Perhaps newer versions of USEARCH do this automatically.

```bash
usearch -uchime_ref allsamples_otus.fa \
-db ./seq_datases/gold.fa \
-strand plus \
-nonchimeras allsamples_otus_chimslay.fa
```

Label OTU sequences OTU_1, OTU_2... The python scripts for this are the same referenced above.

```bash
python ./python_scripts/fasta_number.py \
allsamples_otus_chimslay.fa OTU_ > allsamples_otus_final.fa
```

Map reads (including singletons) back to OTUs

```bash
usearch -usearch_global allsamples.fa \
-db allsamples_otus_final.fa \
-strand plus \
-id 0.97 \
-uc allsamples_map.uc
```

Now create an OTU table. The python scripts for this are the same referenced above.

```bash
python ./python_scripts/uc2otutab.py \
allsamples_map.uc > allsamples_otu_table.txt
```