---
layout: page
title: 
permalink: /TM_roseo_sar/annotation_pipeline/1_makeprofiledb/
---
### Generate RPSblast profile databases for a collection of protein families


```python
import subprocess
```

need a folder containing some version of the NCBI CDD database 'localrpsb_path'


```python
localrpsb_path = '/home/shane/Documents/localrpsb/cdd_v3.14'
project_path = '/home/shane/Documents/grad_classes/SIO209_Python_2015/homework/finalproject'
```

#### Call makeprofile db for each protein family


```python
threshold = '9.82'
dbtype = 'rps'
scale = '100.0' #tab format
index = 'true'

protfams = open(project_path+'/prot_fams')

for fam in protfams:
    fam = fam.rstrip()
    pnfile = localrpsb_path+'/pns/'+fam+'.pn' 
    dbfile = localrpsb_path+'/db/reduced/'+fam
    #cwd = localrpsb_path+'/smps'
  
    subprocess.call(['makeprofiledb', 
                     '-in', pnfile, 
                     '-out', dbfile, 
                     '-title', fam, 
                     '-threshold', threshold],
                     '-scale', scale],
                     '-dbtype', dbtype],
                     '-index', index]],
                   cwd=localrpsb_path+'/smps')

protfams.close()
```
