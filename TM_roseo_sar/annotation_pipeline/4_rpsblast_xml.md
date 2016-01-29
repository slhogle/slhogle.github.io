---
layout: page
title: 
permalink: /TM_roseo_sar/annotation_pipeline/4_rpsblast_xml/
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
seg = 'no'
outfmt = '5' #tab format
targseq = '10'
dbloc = localrpsb_path+'/db/full/Cdd'

protfams = open(project_path+'/prot_fams')

for fam in protfams:
    fam = fam.rstrip()
    qloc = project_path+'/1st_blasts/'+fam+'_uniq_76roseo.faa'
    outloc = project_path+'/2nd_blasts/'+fam+'_76roseo.xml'
    
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
