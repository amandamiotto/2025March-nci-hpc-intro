---
title: "Using resources effectively"
teaching: 10
exercises: 20
questions:
- "How can I review past jobs?"
- "How can I use this knowledge to create a more accurate submission script?"
objectives:
- "Look up job statistics."
- "Make more accurate resource requests in job scripts based on data describing past performance."
keypoints:
- "Accurate job scripts help the queuing system efficiently allocate
  shared resources."
---

We've touched on all the skills you need to interact with an HPC cluster:
logging in over SSH, loading software modules, submitting parallel jobs, and
finding the output. Let's learn about estimating resource usage and why it
might matter.

## Estimating Required Resources Using the Scheduler

Although we covered requesting resources from the scheduler earlier with the
π code, how do we know what type of resources the software will need in
the first place, and its demand for each? In general, unless the software
documentation or user testimonials provide some idea, we won't know how much
memory or compute time a program will need.

> ## Read the Documentation
>
> Most HPC facilities maintain documentation as a wiki, a website, or a
> document sent along when you register for an account. Take a look at these
> resources, and search for the software you plan to use: somebody might have
> written up guidance for getting the most out of it.
{: .callout}

A convenient way of figuring out the resources required for a job to run
successfully is to submit a test job, and then ask the scheduler about its
impact using `qstat -fH`. You can use this knowledge to set up the
next job with a closer estimate of its load on the system. A good general rule
is to ask the scheduler for 20% to 30% more time and memory than you expect the
job to need. This ensures that minor fluctuations in run time or memory use
will not result in your job being cancelled by the scheduler. Keep in mind that
if you ask for too much, your job may not run even though enough resources are
available, because the scheduler will be waiting for other people's jobs to
finish and free up the resources needed to match what you asked for.

## Stats

Since we already submitted `amdahl` to run on the cluster, we can query the
scheduler to see how long our job took and what resources were used. We will
use `qstat -fx` to get statistics about `parallel-job.sh`.

```
{{ site.remote.prompt }} qstat -fH 137011321.gadi-pbs
```
{: .language-bash}
```

gadi-pbs: 
                                                                 Req'd  Req'd   Elap
Job ID               Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
-------------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
137011321.gadi-pbs   am9079   normal-* solo-job   34923*   1   4  3072m 00:30 F 00:00

```
{: .output}


This shows us a summary of our pbs job run. We can get more indepth too - 

```
{{ site.remote.prompt }} qstat -fx 137011321.gadi-pbs
```
{: .language-bash}
```
Job Id: 137011321.gadi-pbs
    Job_Name = solo-job
    Job_Owner = am9079@gadi-login-09.gadi.nci.org.au
    resources_used.cpupercent = 5
    resources_used.cput = 00:00:01
    resources_used.jobfs = 0b
    resources_used.mem = 404316kb
    resources_used.ncpus = 4
    resources_used.vmem = 404316kb
    resources_used.walltime = 00:00:34
    job_state = F
    queue = normal-exec
    server = gadi-pbs-01.gadi.nci.org.au
    Checkpoint = u
    ctime = Fri Mar 14 16:57:24 2025
    Error_Path = gadi.nci.org.au:/home/578/am9079/solo-job.e137011321
    exec_host = gadi-cpu-clx-2119/21*4
    exec_vnode = (gadi-cpu-clx-2119:ncpus=4:mem=3145728kb:jobfs=102400kb)
    group_list = cd82
    Hold_Types = n
    Join_Path = n
    Keep_Files = n
    Mail_Points = a
    mtime = Fri Mar 14 16:58:41 2025
    Output_Path = gadi.nci.org.au:/home/578/am9079/solo-job.o137011321
    Priority = 0
    qtime = Fri Mar 14 16:57:24 2025
    Rerunable = False
    Resource_List.jobfs = 104857600b
    Resource_List.mem = 3221225472b
    Resource_List.mpiprocs = 4
    Resource_List.ncpus = 4
    Resource_List.nodect = 1
    Resource_List.place = free
    Resource_List.select = 1:ncpus=4:mpiprocs=4:mem=3221225472:job_tags=normal:
	jobfs=104857600
    Resource_List.storage = scratch/cd82
    Resource_List.walltime = 00:30:00
    Resource_List.wd = 1
    stime = Fri Mar 14 16:57:57 2025
    obittime = Fri Mar 14 16:58:41 2025
    session_id = 3492344
    jobdir = /home/578/am9079
    substate = 92
    Variable_List = PBS_O_HOME=/home/578/am9079,PBS_O_LANG=en_AU.UTF-8,
	PBS_O_LOGNAME=am9079,
	PBS_O_PATH=/home/578/am9079/.local/bin:/home/578/am9079/bin:/opt/pbs/d
	efault/bin:/opt/nci/bin:/opt/bin:/opt/Modules/v4.3.0/bin:/bin:/usr/bin:
	/usr/local/sbin:/usr/sbin:/opt/pbs/default/bin,
	PBS_O_MAIL=/var/spool/mail/am9079,PBS_O_SHELL=/bin/bash,
	PBS_O_TZ=:/etc/localtime,PBS_O_INTERACTIVE_AUTH_METHOD=resvport,
	PBS_O_HOST=gadi-login-09.gadi.nci.org.au,
	PBS_O_WORKDIR=/home/578/am9079,PBS_O_SYSTEM=Linux,PROJECT=cd82,
	PBS_NCI_HT=0,PBS_NCI_STORAGE=scratch/cd82,PBS_NCI_IMAGE=,PBS_NCPUS=4,
	PBS_NGPUS=0,PBS_NNODES=1,PBS_NCI_NCPUS_PER_NODE=48,
	PBS_NCI_NUMA_PER_NODE=4,PBS_NCI_NCPUS_PER_NUMA=12,PBS_VMEM=3221225472,
	PBS_NCI_WD=1,PBS_NCI_JOBFS=104857600b,PBS_NCI_LAUNCH_COMPATIBILITY=0,
	PBS_NCI_FS_GDATA1=0,PBS_NCI_FS_GDATA1A=0,PBS_NCI_FS_GDATA1B=0,
	PBS_NCI_FS_GDATA2=0,PBS_NCI_FS_GDATA3=0,PBS_NCI_FS_GDATA4=0,
	PBS_O_QUEUE=normal,PBS_JOBFS=/jobfs/137011321.gadi-pbs
    comment = Job run at Fri Mar 14 at 16:57 on (gadi-cpu-clx-2119:ncpus=4:mem=
	3145728kb:jobfs=102400kb) and finished
    etime = Fri Mar 14 16:57:24 2025
    run_count = 1
    Stageout_status = 1
    Exit_status = 0
    Submit_arguments = serial-job.sh
    history_timestamp = 1741931921
    project = cd82
    Submit_Host = gadi-login-09.gadi.nci.org.au

```
{: .output}

It will show a lot of info; in fact, every single piece of info collected on
your job by the scheduler will show up here. 

> ## Discussion
>
> This view can help compare the amount of time requested and actually
> used, duration of residence in the queue before launching, and memory
> footprint on the compute node(s).
>
> How accurate were our estimates?
{: .discussion}

## Improving Resource Requests

From the job history, we see that `amdahl` jobs finished executing in
at most a few minutes, once dispatched. The time estimate we provided
in the job script was far too long! This makes it harder for the
queuing system to accurately estimate when resources will become free
for other jobs. Practically, this means that the queuing system waits
to dispatch our `amdahl` job until the full requested time slot opens,
instead of "sneaking it in" a much shorter window where the job could
actually finish. Specifying the expected runtime in the submission
script more accurately will help alleviate cluster congestion and may
get your job dispatched earlier.

> ## Narrow the Time Estimate
>
> Edit `parallel_job.sh` to set a better time estimate. How close can
> you get?
>
> Hint: use `{{ site.sched.flag.time }}`.
>
> > ## Solution
> >
> > The following line tells {{ site.sched.name }} that our job should
> > finish within 2 minutes:
> >
> > ```
> > {{ site.sched.comment }} {{ site.sched.flag.time }}{% if site.sched.name == "Slurm" %} {% else %}={% endif %}00:02:00
> > ```
> > {: .language-bash}
> {: .solution}
{: .challenge}


Another really good resource for benchmarking and optimisation can be found [at this good code etiquette page](https://github.com/CurtinIDS/good-code-etiquette).

{% include links.md %}
