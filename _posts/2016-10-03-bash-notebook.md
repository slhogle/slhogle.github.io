---
layout:     post
title:      Bash commands
date:       2016-12-03
summary:    A notebook of my bash commands
---
Text form can be found at my [github](https://github.com/slhogle/scripts)

{% highlight bash %}
#!/usr/bin/env bash

# Makes bioperl index file of GOS CN assembly
bp_index.pl -dir . -fmt fasta GOS_ass_CN_index /usr/local/depot/projects/GOS/analysis/jgoll-CN_2012_92pct-annotation/metagene_seq.faa

# loops through txt file with 1 accession per line and extracts them using bp_fetch.pl
while read accession_number
do
  bp_fetch net::${accession_number}
done < accessions.list > results.txt

# reads an ID file containing scaffold IDs of TRAP SBP, 
# then loops through *all* scaffold IDs with the annotation 'TRAP' 
# and counts the number of occurences each SBP scaffold has in that file. 
while read accession_number
do
  grep -c "${accession_number}" TRAP_CN_total_assembly
done < TRAP_SBP_scf_IDs.formatted.list > results.txt

#replaces any instance of one or more whitespace 
# characters with a tab (the g is for global)
sed -r 's/\s+/\t/g' rest.out > rest1.out

#removes one or more whitespace only at the end of each line
sed -r 's/\s+$//' 1-3 > 1-3.out

#removes one or more whitespace only at the beginning of each line
sed -r 's/^\s+//' rest > rest.out

#Separate file by multiple delimiters - in this case it is colon and semicolon
awk -F '[>...]' '{print $2}' file

#Take a uclust output file, separate spaces by tab, 
# select the second column, remove ">" from that column, 
# remove "..." from the column, print output
sed -r 's/\s+/\t/g' art | awk -F '[\t]' '{print $2}' | sed -r 's/>//' | sed -r 's/\.\.\.//' > out

#Remove all rows with a . in the 7th column
awk '$7 == "." { next } { print }' "$file"

#Add Cluster_ to begining of each line in file
sed -r 's/^/Cluster_/' test > test.new

# Rename all files with *_05242016.fna 
# to *.fna
for f in *_05242016.fna
do
  mv -- "$f" "${f%_05242016.fna}.fna"
done

# Delete all files in a list called "remove_list.txt"
while read -r entry
do 
  rm ${entry}
done < remove_list.txt

# count the files in every subdirectory in a directory
for D in */
do
  echo -n $D" has this many entries "
  ls $D | wc -l
done

## RENAME all files in directory 
# from scB241_528N20.contigs.fna to scB241_528N20.fna
rename .contigs.fna '.fna' *

## RENAME all files in directory 
# from scB241_528N20.fna to B241_528N20.fna
rename scB 'B' *

## Gets file extensions and prepending paths
for f in /nobackup1/shogle/pro_genomes/sags/simons/*
do
  path=${f%.fna}
  echo ${path}
  name=${path##*/}
  echo ${name}
done

#split fasta file into files with single fasta entry
while read line
do
    if [[ ${line:0:1} == '>' ]]
    then
        outfile=${line#>}.fa
        echo $line > $outfile
    else
        echo $line >> $outfile
    fi
done < tmp.fasta

## find lines that exist in file "all" that don't exist in file "have"
comm -13 <(sort have) <(sort all) > ids_4_phylosift.txt

## show differences between file "have" and "all" in 
# side by side format and with width of 72 characters
diff -y -W 72 <(sort have) <(sort all)

## parse the ncbi taxonomy tree (nodes.dmp) searching for a (partial) list of ids in file names.txt
while read entry
do
  grep ${entry} names.dmp | gsed 's/\t//g' | gcut -d "|" -f1,2 | gsed 's/|/\t/g'
done < names.txt > filtered_pro.txt

{% endhighlight %}