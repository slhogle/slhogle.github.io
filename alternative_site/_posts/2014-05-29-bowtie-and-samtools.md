---
layout:     post
title:      Bowtie and Samtools
date:       2014-05-29
summary:    Bowtie is an excellent tool for mapping sequencing reads to long reference sequences, such as the human genome. Here I'll give an introduction to using Bowtie and the great utility Samtools
categories: bioinformatics_notebook
comments: true
---
A little out of character for me as I usually work on environmental genomics. I've been working on RNAseq data from human stem cells subjected to different treatments of WNT signaling proteins. We are interested in looking for differential expression of certain genes under the different treatment conditions. One great tool for doing this is [bowtie](http://bowtie-bio.sourceforge.net/index.shtml) or [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml), which is a very fast and efficient short read aligner. In my case I am using bowtie (not bowtie2) to map the mRNA sequences to the human genome (version hg19). There are a variety of reasons to choose between bowtie and bowtie2 that I won't go into here. By using a series of other tools in the bowtie family, the resulting mapping are compared to one another and we can search for differences between locations on the genome where mRNA sequences mapped.

Right now I'll walk you through some commands I've used to take a bunch of sequence files and a reference database of the human genome and spit out coordinates of the human genome where these sequences mapped.

First off, Bowtie needs something called a genome index. You can either build this yourself or there are lots of prebuilt indexes [here](http://support.illumina.com/sequencing/sequencing_software/igenome.html). Say you're working on a genome that doesn't have a prebuilt index. You can make one yourself just from the fasta files of that genome. For example...
{% highlight bash %}
./bowtie-build [options]* <reference_in> <ebwt_base>
./bowtie-build -f chr1.fa,chr2.fa,chr3.fa,chr4.fa,chr5.fa... hg19
{% endhighlight %}

OK so now you've got your Bowtie-formatted genome index. In my case it is called "hg19." You can map a variety of sequence file types to your genome index and these file types are indicated by different flags. In my case, I have a file of what's called "raw sequences" this is one sequence per line with no header of any kind per sequence. To tell bowtie what kinds of sequences you need to use different flags: -r is for raw sequences, -f is for old fashioned fasta files, -q is for fastq files. If your sequences are raw or fasta formatted, bowtie gives them a default Phred quality score of 40.
{% highlight bash %}
bowtie -r -n 3 -l 50 -m 1 -S ~/FILEPATH/hg19 ~/FILEPATH/mRNASEQNAME.raw ~/FILEPATH/MYmRNAOUTPUT.sam
{% endhighlight %}

The -r flag tells bowtie to expect raw sequences, the -n flag denotes the maximum number of mismatches permitted in the "seed," the -l flag denotes the "seed length"; i.e., the number of bases on the high-quality end of the read to which the -n ceiling applies, and the the -m flag tells bowtie to suppress all alignments for a particular read if more than X reportable alignments exist for it. The -S flag tells bowtie to write the output in SAM format. In our case we want bowtie to write out all alignments using a seed length of 50 bases allowing no more than 3 mismatches in those 50 bases, and if a sequence maps to more than one place in the reference genome under those -n and -l conditions to not report any of those alignments. In my experience these are good mapping conditions to start with.

SAM files are BIG. If you find yourself doing many of these kinds of bowtie analyses you will quickly see that the SAM files start to take up many gigabytes of disk space. A solution to the SAM size issue is to convert the SAM output from bowtie to BAM format. BAM is a binary format that takes up less space than the text content of SAM and can be passed to downstream tools later. Bowtie does not write to BAM format so you need to use SAMtools to do this. An efficient method that I like to use to get BAM format in one step is to pipe the output of bowtie to [Samtools](http://samtools.sourceforge.net/) like so:
{% highlight bash %}
bowtie -r -n 3 -l 50 -m 1 -S ~/FILEPATH/hg19 ~/FILEPATH/mRNASEQNAME.raw | samtools view -bSF4 - > ~/FILEPATH/MYmRNAOUTPUT.bam
{% endhighlight %}

The first part of this is the same as above but omitting the output file name. We then use a pipe to send the output to samtools. We use the "view" mode to extract/print all or sub alignments in SAM or BAM format. The flags -bSF4 mean to output in bam format while expecting input to be in SAM format, while also omitting any alignments that have the "4" flag which indicates that the query sequence itself is unmapped to the reference genome. This output format is useful so the downstream analyses don't get clogged up with unmapped reads.

Next post I'll show you how to send some BAM files to the cuffdiff tool to find differences between the two files - ie) differences in gene expression.
