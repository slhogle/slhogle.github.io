---
layout: page
title: 
permalink: /5_rpsbproc/
---

## 2nd rpsblast loop - rpsbproc parse xml output
I don't understand why, but you must call rpsbproc from within '/home/shane/Documents/localrpsb/cdd_v3.14' in order for it to work... You can't use the convenient Python subprocess.call() 'cwd' parameter


```python
import subprocess
```


```python
localrpsb_path = '/home/shane/Documents/localrpsb/cdd_v3.14'
project_path = '/home/shane/Documents/grad_classes/SIO209_Python_2015/homework/finalproject'
```


```python
cd /home/shane/Documents/localrpsb/cdd_v3.14/
```


```python
protfams = open(project_path+'/prot_fams')

t = 'doms'
ecutoff = '1e-5'

for fam in protfams:
    fam = fam.rstrip()
    inloc = project_path+'/2nd_blasts/'+fam+'_76roseo.xml' 
    outloc = project_path+'/2nd_blasts/'+fam+'_76roseo.rpsbproc'
    
    subprocess.call(['rpsbproc',
                     '-i', inloc,
                     '-o', outloc,
                     '-t', t, '-e', ecutoff])
    
protfams.close()
```
