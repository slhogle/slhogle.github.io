---
layout:     post
title:      Jupyter notebook on remote cluster
date:       2017-02-11
summary:    How to run Jupyter notebook on remote cluster (slurm)
---
### INTRODUCTION
[Jupyter notebook](http://jupyter.org/) is a handy little system for running and documenting your code. You can run it on a remote cluster from your local workstation. Here's how!

[Jupyter docs here](https://jupyter.readthedocs.io/en/latest/index.html)

Based off instructions [here](https://wikis.uit.tufts.edu/confluence/display/TuftsUITResearchComputing/Jupyter)

#### Get an interactive terminal
{% highlight bash %}
srun --pty -p sched_mit_NNNNN bash
{% endhighlight %}

Note the node that you are allocated by the slurm scheduler. In this case it is
{% highlight bash %}
node421
{% endhighlight %}

#### Start Jupyter notebook on your node
{% highlight bash %}
jupyter notebook --no-browser --port=8888
{% endhighlight %}

#### Open ssh conncetion to cluster and node421
{% highlight bash %}
ssh -t -t myname@eofe4.mit.edu -L 8888:localhost:8888 ssh node421 -L 8888:localhost:8888
{% endhighlight %}

#### Tunnel from local machine to Jupyter notebook
Now direct your browser on your local machine to [http://localhost:8888/](http://localhost:8888/). You should now see the Jupyter notebook view of the filesystem on the cluster.

#### Don't forget to close connection when finished
{% highlight bash %}
ctrl-c
{% endhighlight %}
