---
layout:     post
title:      Slurm Cheatsheet
date:       2016-12-04
summary:    Useful commands for using the SLURM scheduler
---
- General SLURM documentation found [here](https://slurm.schedmd.com/)
- SLURM [tutorials](https://slurm.schedmd.com/tutorials.html)
- SLURM tools [youtube video](https://www.youtube.com/watch?v=U42qlYkzP9k&feature=player_embedded)
- SLURM [tutorial](https://rc.fas.harvard.edu/resources/running-jobs/) @ Harvard

------------
### Submitting and cancelling SLURM jobs
Submit a job script called my_script.sh requesting:
- 5GB RAM per cpu (--mem-per-cpu 5GB)
- 20 CPUs on a single node (-c 20)
- use the scheduler queue $MYQUEUE (-p $MYQUEUE)
- use the job name $MYJOB_NAME (-J $MYJOB_NAME)
- provide a time ceiling of 3 HRs (-t 0-3:00:00)
- write STDOUT to file $MYLOG.log (-o $MYLOG.log)

{% highlight bash %}
sbatch --mem-per-cpu 5GB -c 20 -p $MYQUEUE -J $MYJOB_NAME -t 0-3:00:00 -o $MYLOG.log my_script.sh

# Cancel a task with the related JOB_ID
scancel $MYJOB_ID
{% endhighlight %}

Submit an interactive job
- 2GB RAM per cpu (--mem-per-cpu 2GB)
- 4 CPUs on a single node (-c 4)
- use the scheduler queue $MYQUEUE (-p $MYQUEUE)
- provide a time ceiling of 1 HR (-t 0-1:00:00)
- execute task zero in pseudo terminal mode (--pty). The option --pty is important because it allows an interactive terminal mode. Without --pty every command issued would be run 4 times (-c 4)

{% highlight bash %}
srun --mem-per-cpu 2GB -c 4 -p $MYQUEUE -t 0-01:00:00 --pty /bin/bash

# Use srun for any long jobs, even cp or rsync
# DONT USE THE LOGIN NODE
srun -p $MYQUEUE cp my_file my_new_file
{% endhighlight %}

### Jobs can be submitted by passing all SLURM parameters through bash script 

{% highlight bash %}
#!/usr/bin/env bash
#SBATCH --mem-per-cpu 5GB
#SBATCH -c 20
#SBATCH -p $MYQUEUE
#SBATCH -J $MYJOB
#SBATCH -t 0-3:00:00
#SBATCH -o $MYLOG.log my_script.sh

raxmlHPC-PTHREADS-AVX -T 20 \
-m GTRGAMMA \
-p 82748 \
-# 20 \
-s $MY_PHYLIP_FILE \
-n $MY_NAME \
-o $MY_OUTGROUP \
-w $MY_OUTDIR
{% endhighlight %}

Run the RAxML analysis as
{% highlight bash %}
sbatch my_raxml_script.sh
{% endhighlight %}

### Find information about partitions and jobs

{% highlight bash %}
# Display submitted jobs for a given user
squeue -u $USER
{% endhighlight %}

List job information
- The sacct command displays job accounting data stored in the job accounting log file or Slurm database in a variety of forms for your analysis. The sacct command displays information on jobs, job steps, status, and exitcodes by default. For the non-root user, the sacct command limits the display of job accounting data to jobs that were launched with their own user identifier (UID) by default. Data for other users can be displayed with the --allusers, --user, or --uid options.

{% highlight bash %}
sacct --format="CPUTime,MaxRSS,AveRSS,JobName,Timelimit,Start,Elapsed"

# Display available partitions on the cluster
sinfo

# List jobs that ran since Dec 1st 2016
sacct -S 2016-12-01

# Get help about sacct command
sacct --helpformat
{% endhighlight %}