---
layout:     post
title:      Process substitution with bash
date:       2014-04-16
summary:    Increase speed and productivity with process subsitution
categories: bioinformatics_notebook
comments: true
---

From [Wikipedia](http://en.wikipedia.org/wiki/Process_substitution) - _"Process substitution is a form of inter-process communication that allows the input or output of a command to appear as a file. The command is substituted in-line, where a file name would normally occur, by the command shell. This allows programs that normally only accept files to directly read from or write to another program."_

Say you want to use join to find and count the differences in two files each containing lines of biological sequence but those files are not sorted.

You could do
{% highlight bash %}
sort seqfile1 > seqfile1.sorted
sort seqfile2 | join -v 2 - seqfile1.sorted | wc -l
{% endhighlight %}

This would give you the number of unmatched entries in seqfile1 when comparing it with seqfile2

One can also do this using process substitution
{% highlight bash %}
join -v 2 <(sort seqfile1) <(sort seqfile2)  | wc -l
{% endhighlight %}
