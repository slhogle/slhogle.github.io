---
layout:     post
title:      Fasta extracta
date:       2014-03-11
summary:    Output sequences from a fasta formatted file that match a list of sequence ids
categories: bioinformatics_notebook
comments: true
---
You need bioruby for this one. [Bioruby](http://bioruby.org/) is quite easy to install on unix systems. I use Ubuntu... The script pulls the fasta entries from a multi-fasta file based on a provided list of sequence ids.

{% highlight ruby %}
#!/usr/bin/env ruby
require 'bio'
include Bio
id_file = ARGV[0]
seq_file = ARGV[1]
ids = Array.new
File.new(id_file, 'r').each { |line| ids << line.chop }
File.open(id_file+'_fasta_extracted', 'w') do |file|
  Bio::FlatFile.auto(seq_file).each do |item|
    ids.each do |id|
      if item.definition =~ /#{id}/
        seq = Bio::FastaFormat.new('> '+item.definition+"\n"+item.data)
        file.puts seq
      end
    end
  end
end
{% endhighlight %}
