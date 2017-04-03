---
layout:     post
title:      Slurm no-number array trick
date:       2017-02-11
summary:    Running an array job on slurm when filenames aren't ordered numerically
---
### INTRODUCTION
Slurm can parallelize jobs very nicely if each file has some form of a number in it's title. For example if your files look like "MYPREFIX0004.txt, MYPREFIX0005.txt, MYPREFIX0006.txt, ect" you could do:

{% highlight bash %}
mylib=MYPREFIX`printf %04d $SLURM_ARRAY_TASK_ID`
{% endhighlight %}

Then just use the $mylib variable in your script and call the script like so:

{% highlight bash %}
sbatch --array 5-6 myscript.sbatch
{% endhighlight %}

which will have the slurm scheduler submit parallel jobs for you to make your jobs run quicker.

If your file aren't formatted with a number you can use this workaround.

Glob the filenames you want to run operations on from whatever directory
{% highlight bash %}
FILES=(/mypath/*)
{% endhighlight %}

Create var NUMFILES which will be the length of the submitted array job
{% highlight bash %}
NUMFILES=${#FILES[@]}
echo $NUMFILES
{% endhighlight %}

Create a variable in your script that references an array value using $SLURM_ARRAY_TASK_ID as an index
{% highlight bash %}
entry=$(basename ${FILES[$SLURM_ARRAY_TASK_ID]} .tab) #include file extension if you want to cut that off
{% endhighlight %}
