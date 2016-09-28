---
layout:     post
title:      Nucleotide content of 1st and 2nd positions
date:       2014-04-15
summary:    Get the average nucleotide content of the first and second position in a .fastq file
categories: bioinformatics_notebook
comments: true
---

So this writes out the frequency of all nucleotides at the first and second position in a fastq formatted file. You need to install the bio-faster package. This can be done by as follows:

{% highlight bash %}
sudo gem install bio-faster
sudo gem update bio
{% endhighlight %}

You can use input as a gnu zipped file. Call it like
{% highlight bash %}
zcat test.fastq.gz | ruby fastq_reader.rb
{% endhighlight %}

Now for the script.
{% highlight ruby %}
#!/usr/bin/env ruby
require 'bio'
require 'bio-faster'
include Bio
ary_bp1 = Array.new
ary_bp2 = Array.new
counts_bp1 = Hash.new(0)
counts_bp2 = Hash.new(0)
fastq = Bio::Faster.new(:stdin) # if stdin then you can pipe output from zcat -ex) zcat test.fastq.gz | ruby fastq_reader.rb
fastq.each_record do |sequence_header, sequence, quality|
  seq = Bio::Sequence.auto(sequence)
  ary_bp1 << sequence[0]
  ary_bp2 << sequence[1]
end
ary_bp1.each { |base| counts_bp1[base] += 1 }
ary_bp2.each { |base| counts_bp2[base] += 1 }
puts counts_bp1
puts counts_bp2
{% endhighlight %}
