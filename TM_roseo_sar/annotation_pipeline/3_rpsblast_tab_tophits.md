---
layout: page
title: 
permalink: /TM_roseo_sar/annotation_pipeline/3_rpsblast_tab_tophits/
---

```python
from Bio import SeqIO
roseo_db = SeqIO.index_db("76roseo_db.idx", '76roseo_db.faa', "fasta")
print("%i sequences indexed" % len(roseo_db))
```

    316391 sequences indexed



```python
localrpsb_path = '/home/shane/Documents/localrpsb/cdd_v3.14'
project_path = '/home/shane/Documents/grad_classes/SIO209_Python_2015/homework/finalproject'
```


```python
def poormans_deduplicate(infile):
    filein = open(infile)
    ids = []
    
    for line in filein:
        query = line.split("\t")[0]
        ids.append(query)
        
    uniq_ids = list(set(ids))
    
    filein.close()
    return uniq_ids
```


```python
protfams = open(project_path+'/prot_fams')

for fam in protfams:
    fam = fam.rstrip()
    blast_tab_path = '1st_blasts/'+fam+'_76roseo.tab'
    fileout = open('1st_blasts/'+fam+'_uniq_76roseo.faa', 'w')
    
    fam_uniq_ids = poormans_deduplicate(blast_tab_path)
    
    for ids in fam_uniq_ids:
        fileout.write(">%s\n%s\n" % 
		(roseo_db[ids].description, roseo_db[ids].seq))
    
    fileout.close()
    
protfams.close()
```
