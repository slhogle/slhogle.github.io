---
layout:     post
title:      Installing the bash kernel for Jupyter notebook
date:       2017-01-19
summary:    How to make a jupyter notebook that runs bash commands/scripts
---
### INTRODUCTION
[Jupyter notebook](http://jupyter.org/) is a handy little system for running and documenting your code. I use it frequently for my python 2.7 code, but I also write a lot of code in bash. I was using emacs org-mode to write and document my bash scripts, but I recently decided to port them to jupyter notebook. Here's how to do it!

[Jupyter docs here](https://jupyter.readthedocs.io/en/latest/index.html)

#### Setting up python environment
If you haven't already used it, [Anaconda](https://www.continuum.io/anaconda-overview) is a pretty useful system for managing different python installations and environments. Here's how you install it (python 2.7 version) for OSX:

{% highlight bash %}
wget https://repo.continuum.io/archive/Anaconda2-4.3.0-MacOSX-x86_64.sh
bash Anaconda2-4.3.0-MacOSX-x86_64.sh 
{% endhighlight %}

#### Installing dependencies
The bash kernel for Jupyter notebook requires python 3. Because I do a lot of bioinformatics stuff that relies on python 2.7 I installed that Anaconda version. However, we can easily get python 3.X. Here's how:

{% highlight bash %}
# Create the environment using Python 3 and add the IPython and Juypter system with notebook support.
conda create -n py3k python=3 ipython notebook
# now you need to activate the python 3 environment
source activate py3k
{% endhighlight %}

#### Installing the bash kernel
The new bash kernel can be installed using pip (already installed with anaconda)
{% highlight bash %}
pip install bash_kernel
python -m bash_kernel.install
source deactivate py3k
{% endhighlight %}

#### Running the notebook
{% highlight bash %}
# Remember to activate the python 3 env first!
source activate py3k
# start up the notebook server
jupyter notebook
# when you're finished deactivate the python 3 env so we can default to python 2.7
source deactivate py3k
{% endhighlight %}

Now bash should be an available kernel for working in your notebooks. Good to go!