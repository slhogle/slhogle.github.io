---
layout: page
title: 
permalink: /TM_roseo_sar/annotation_pipeline/2_rpsblast_tab/
---

```python
import subprocess
```


```python
localrpsb_path = '/home/shane/Documents/localrpsb/cdd_v3.14'
project_path = '/home/shane/Documents/grad_classes/SIO209_Python_2015/homework/finalproject'
```


```python
threads = '4'
evalue = '0.00001'
outfmt = '6' #tab format
targseq = '10'
qloc = project_path+'/76roseo_db.faa'

protfams = open(project_path+'/prot_fams')

for fam in protfams:
    fam = fam.rstrip()
    dbloc = localrpsb_path+'/db/reduced/'+fam 
    outloc = project_path+'/1st_blasts/'+fam+'_76roseo.tab'
    
    subprocess.call(['rpsblast',
                     '-db', dbloc,
                     '-query', qloc,
                     '-out', outloc,
                     '-num_threads', threads,
                     '-evalue', evalue,
                     '-outfmt', outfmt,
                     '-max_target_seqs', targseq])
    
protfams.close()
```
