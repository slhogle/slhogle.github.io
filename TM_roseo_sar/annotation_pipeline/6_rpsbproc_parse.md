---
layout: page
title: 
permalink: /TM_roseo_sar/annotation_pipeline/6_rpsbproc_parse/
---

```python
import re
import pandas as pd
import numpy as np
```


```python
localrpsb_path = '/home/shane/Documents/localrpsb/cdd_v3.14'
project_path = '/home/shane/Documents/grad_classes/SIO209_Python_2015/homework/finalproject'
```


```python
def extract_query(line):
    query = line.split("\t")[4].rstrip()
    m = re.search('([0-9]+)_(.+)', query)
    i = m.group(1) # protein ID from IMG
    j = m.group(2) # organism/genome name
    return(i, j)

def extract_domain_info(line):
    row = line.split("\t")
    k = row[2] # hit type
    l = row[8] # domain ID of hit
    m = row[9] # domain name of hit
    return(k, l, m)
```


```python
protfams = open(project_path+'/prot_fams')

for fam in protfams:
    fam = fam.rstrip()
    filename_in = project_path+'/2nd_blasts/'+fam+'_76roseo.rpsbproc'
    filename_out = project_path+'/2nd_blasts/'+fam+'.counts'
    
    file_in = open(filename_in)
    file_out = open(filename_out, 'w')
    file_out.write('prot_id\tgenome\thit_type\tdom_id\tdom_name\n')
    
    for line in file_in:
        if line.startswith('QUERY'):
            prot_id, genome = extract_query(line)
            line = next(file_in)
        else: 
            continue
        
        if line.startswith('DOMAINS'):
            line = next(file_in)
            #domain_annot = []
            hit_type, dom_id, dom_name = extract_domain_info(line)
            file_out.write("%s\t%s\t%s\t%s\t%s\n" % (prot_id, genome, hit_type, dom_id, dom_name))
            line = next(file_in)
        else:
            continue
    
    file_in.close()
    file_out.close()

protfams.close()
```


```python
protfams = open(project_path+'/prot_fams')
all_genomes = pd.read_csv('full_genome_list', sep='\t', header=None).values

for fam in protfams:
    fam = fam.rstrip()
    filename_in = project_path+'/2nd_blasts/'+fam+'.counts'
    filename_out = project_path+'/final_tables/'+fam+'_wideform.tsv'
    
    count_table = pd.read_csv(filename_in, sep='\t', header=0)
    grouped = count_table.groupby(['genome', 'dom_name'])
    grouped = grouped.size().reset_index()
    grouped = grouped.rename(columns={0: 'counts'})
    grouped = grouped.pivot(index='genome', columns='dom_name', values='counts')
    grouped_genomes = grouped.index.values
    
    diff = np.setdiff1d(all_genomes, grouped_genomes, assume_unique=True)
    diff = pd.DataFrame(diff)
    diff = diff.set_index(0)
    
    grouped_final = grouped.append(diff).fillna(0)
    grouped_final.to_csv(filename_out, sep='\t')
        
protfams.close()
```
