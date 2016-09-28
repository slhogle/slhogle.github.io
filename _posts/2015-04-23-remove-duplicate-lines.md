---
layout:     post
title:      Collecting top hits
date:       2015-04-23
summary:    AWK one liner for removing duplicate entries in a delimited file
categories: bioinformatics_notebook
comments: true
---
Many times in bioinformatics you'll have searched many sequences against a database (for example hmmscan versus pfamA). Say you are interested in only retaining the highest scoring PFAM hits for each of your query sequences. Depending on your cutoff score and the number of expected protein domains, each sequence may have gotten hits to multiple different PFAMs. For example, feoB transporters have GTPase-like binding domains, and it is easy to mis-annotate other sequences with GTPase domains as feoB proteins. 

To reduce a hmmscan output file to only the best scoring PFAMs you could use:
{% highlight bash %}
awk '!x[$3]++' MYOUTFILE.pfam  > MYBESTHITS.pfam
{% endhighlight %}
and you would get only the lines containing the PFAMs with highest full-sequence score.

This code takes advantage of the fact that the output of hmmscan is already ordered with the highest scoring domain first for each sequence. In short, after it sees the first unique "query name" entry it removes all subsequent lines that have that "query name" entry then moves on to the next. If the sequences weren't sorted with highest scoring PFAMs appearing first this wouldn't give you the top hits - it would just de-duplicate your file. To make this work on other kinds of files you would need to sort them based on whatever value you are interested in.

For example you could write:
{% highlight bash %}
sort -rnk3 FILE | awk '!x[$2]++' > OUT
{% endhighlight %}

This sorts FILE from highest to lowest (-r) based on column 3 (-k3) and orders it based on string numeric values (-n) then performs awk command on the output.

I learned this little trick off stackoverflow and it is [explained there](http://stackoverflow.com/questions/10842118/explain-this-duplicate-line-removing-order-retaining-one-line-awk-command) much betther than I can do, but I'll take a crack at it anyways...

Basically, what it is doing with our hmmscan output is:

- Read the current line as input
- Index array x with the entry in the "query name" field. If it doesnt already exist create it. Arrays in awk are associative so they are like dictionaries in python.
- Increment value of x[$3] but return the prior value, which will be zero if the "query name" entry has not been seen yet. This is called a postfix increment and is seen for example in C programming.
- Negate the resulting operator so its value is TRUE when x[$3] is zero causing awk to perform the default function which is print the line
- Actually increment so x[$3] is no longer zero. The next time the same entry in the "query name" field is seen, the value of x[$3] = 1.
- The operator is FALSE when x[$3] = 1, so awk does nothing. When a new value for $(3) is seen the process starts over.
