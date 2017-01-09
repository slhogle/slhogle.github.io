---
layout:     post
title:      GNU Parallel for mutlithreaded tasks
date:       2016-12-03
summary:    An example using GNU parallel and PROKKA
---
We're sequencing a bunch of single cell bacterial genomes, and I had like a 1000 genomes we needed to quickly call ORFs on and annotate. I'm using the excellent [Prokka](https://github.com/tseemann/prokka) software, but I wanted to parallelize it with GNU Parallel. I ended up putting together this series of scripts (based off instructions [here](https://rcc.uchicago.edu/documentation/_build/html/running-jobs/srun-parallel/index.html#parallel-batch)) to eventually make it work with our SLURM scheduler. If you know a better way to do this lemme know!

While I'm at it some useful links for GNU Parallel
- [Quick tutorial on Biostars](https://www.biostars.org/p/63816/)
- [Official GNU tutorial](https://www.gnu.org/software/parallel/parallel_tutorial.html)
- [GNU Parallel and SLURM from U Chicago](https://rcc.uchicago.edu/documentation/_build/html/tutorials/kicp-tutorials/running-jobs.html)
- [A nice presentation](http://outreach.ino.pm/presentations/2014/03/genomics-wranglers/index.html#/18) by Ino de Bruijn

-----------
### prokka.hets.sh
{% highlight bash %}
#!/usr/bin/env bash

odir='OUTPATH'
rawnucl='SCAFFOLD_LOCATION'

qpath=$(find ${rawnucl} -name ${1}.fna)

prokka \
--addgenes \
--kingdom Bacteria \
--outdir ${odir}/${1} \
--genus Marine_Heterotroph \
--species ${1} \
--locustag ${1} \
--prefix ${1} \
--cpus 20 ${qpath}
{% endhighlight %}

prokka.hets.sh is the shell script that will be parallelized. The cool thing is that you can run multi-threaded tasks using this techniqe. For example I'm running Prokka using 20 cores per job. GNU Parallel can cleverly handle available resources such that multi-threaded tasks can be parallelized across multiple nodes. More on that below... 

_Of note: $1 is arg1:{1} from parallel._

-----------
### parallel.sh
{% highlight bash %}
#!/usr/bin/env bash

#SBATCH --time=04:00:00
#SBATCH --nodes=10
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=20
#SBATCH --exclusive
#SBATCH -p MYQUEUE

srun="srun --exclusive -N1 -n1 -c$SLURM_CPUS_PER_TASK"

parallel="parallel --delay .2 -j $SLURM_NNODES --joblog runtask.log --resume -a ${2}"

${parallel} "${srun} ./${1}"
{% endhighlight %}

Call this script as:
{% highlight bash %}
sbatch parallel.sh prokka.hets.sh ids.txt
{% endhighlight %}

Where prokka.hets.sh is the script above and ids.txt is a file containing ids (1 per line) of the nucleotide scaffolds that Prokka is to annotate. We use srun to execute the script prokka.hets.sh. The -c$SLURM_CPUS_PER_TASK instructs srun to allocate 20 CPUs to each instance of Prokka that runs.  The --exclusive flag does not let Prokka share nodes with other running jobs and -N1 -n1 mandates that the 20 CPUs be dedicate to a single task (-n1) or a single instance of Prokka that is operating on a single node (-N1). These commands ensure that each Prokka task is confined to running on the 20 cpus residing on a single node.

For the parallel command --delay .2 prevents overloading the controlling node, -j is the number of tasks parallel runs so but instead of $SLURM_NTASKS we want to use $SLURM_NNODES to tell parallel how many jobs to start. --joblog makes parallel create a log of tasks that it has already run and --resume makes parallel use the joblog to resume from where it has left off. The combination of --joblog and --resume allows jobs to be resubmitted if necessary and continue from where they left off. -a ids.txt is an option for parallel that allows it to use ids.txt as input source rather than arguments from the command line passed by ::: which is quite useful if your input doesn't neatly follow some shell expansion or something... 

We finally run the parallel command and prokka.hets.sh should be able to use the 20 cpus per node that we requested with -c20 through srun.