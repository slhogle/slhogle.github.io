---
layout:     post
title:      Unix for fasta
date:       2014-04-14
summary:    Useful unix commands for processing sequence files
categories: bioinformatics_notebook
comments: true
---

The syntax here is using the Bash shell in unix.

The following command counts the number of sequences in a fasta formatted file. The "|" operator is a pipe. This lets you stream the output from one command into another.

{% highlight bash %}
grep ">" YOURFILE.fasta | wc -l
{% endhighlight %}

Extract position 2 through 8 on all sequences in a fasta file. The -v option for grep is the inversion option. This tells grep to only consider lines that are not fasta headers.

{% highlight bash %}
grep -v ">" YOURFILE.fasta | cut -c2-8 > YOURFILE_pos2-8.fasta
{% endhighlight %}

Counts how many unique text strings are in a file.
{% highlight bash %}
sort -u YOURFILE_pos2-8.fasta | wc -l
{% endhighlight %}

Exports counts of unique occurences in YOURFILE_pos2-8.fasta
{% highlight bash %}
sort YOURFILE_pos2-8.fasta | uniq -c > YOURFILE_unique_counts.output
{% endhighlight %}

This series of commands will format YOURFILE\_unique\_counts.output to remove the leading whitespace and replace the remaining whitespace with a tab character "/t" It then will sort this in decreasing numerical order.
{% highlight bash %}
sed 's/^\s\+//' YOURFILE_unique_counts.output | sed 's/\s\+/\t/' | sort -n -r
{% endhighlight %}

__Example questions: Use "grep", "cat", "sort", and "uniq -c" to extract microRNA sequences from [mirbase Version 19](./v19.fa) and [Version 20](./v20.fa) and determine which sequences are in both versions. How many are shared in both versions?__
{% highlight bash %}
grep -v ">" v19.fa | sort -u > v19_uniq.out
grep -v ">" v20.fa | sort -u > v20_uniq.out
{% endhighlight %}

This creates a file (v19\_uniq.out) with only unique nucleic acid sequences. The ">" operator at the end of a command writes the output to a file (in this case v19\_uniq.out) instead of to the screen of the terminal. So now we have both versions of mirbase with only unique sequences - no duplicates - in two different files.
{% highlight bash %}
cat v19_uniq.out v20_uniq.out | sort | uniq -c | grep -c "2"
{% endhighlight %}

The cat command merges the content of files or standard input and outputs to the terminal. So with the first command we combine all unique sequences in each database. We then pipe that output to the sort command which orders the sequences alphanumerically. We pipe that output to uniq -c, which as we saw above counts the number of occurences of a given text string. Now my logic for using a pipe then grep -c "2" is that any sequence that occurs in both version 19 and 20 of mirbase will have a count of 2. (The -c operator can be used with grep to count the number of occurences of a text string or regular expression) If a sequences is unique to either database it will have a count of 1. By counting the number of occurences of 2 we find how many sequences were in both databases. My answer was 13522 sequences.

__Example questions: Use "grep" and "sort" to extract microRNA sequences from mirbase Version 19 and Version 20, creating 2 intermediate files. Use "join" to generate sequences present only in Version 19, only in Version 20, and both versions. How many sequences are in each of the 3 output files?__
{% highlight bash %}
join -v 1 v19_uniq.out v20_uniq.out | wc -l
join -v 2 v19_uniq.out v20_uniq.out | wc -l
{% endhighlight %}

Here I used the same files (v20\_uniq.out and v19\_uniq.out) generated from the previous question. The trick to using join is that your input files must be sorted prior to use. Using the -v X operator tells join to output the lines that are unique to file X. In the way the files are ordered above, -v 1 tells join to ouput only the sequences unique to v19. Using the pipe operator plus wc -l gets the number lines from the join output. So we see that there were 80 unique sequences in mirbase version 19 that were "retired" from the version 20 issue of the database. Likewise, we see that 2204 unique sequences were added to version 20. If you were to use the -a 1 option this would output the shared sequences between the two databases plus the "unmatchable" ones in version 19. Substracting 80 from this number gives you the number of unique sequences shared in both versions.
