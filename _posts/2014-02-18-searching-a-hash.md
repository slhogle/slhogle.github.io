---
layout:     post
title:      Searching a hash
date:       2014-02-18 12:32:18
summary:    Use Ruby to search a hash with the entries from an array
categories: bioinformatics_notebook
comments: true
---

Let's say you have a list of stuff. You also have a tab delimited file with all of the items in your list (plus many more) with some other piece of information associated with each item. If you want to output the information from the tab delimited file associated with the items in your list you can use this script. I don't think this would be very fast on large files (like those approaching 1 GB) the way it is currently coded.

{% highlight ruby %}
#!/usr/bin/env ruby
tab-delimited-mapping = ARGV[0]
list = ARGV[1]
list_array = []
File.new(list, 'r').each { |line| list_array << line.chop }
map = Hash.new
File.open(tab-delimited-mapping).each do |field|
  cols = field.chomp.split("\t")
  map[cols[1]] = cols[0] 
end
File.open(list+'_sub', 'w') do |f|
  list_array.each do |entry|
    if maps.has_key?(entry)
      f.puts maps.fetch(entry)
    else
      f.puts "not found"
    end
  end
end
{% endhighlight %}

