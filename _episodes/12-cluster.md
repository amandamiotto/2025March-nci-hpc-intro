---
title: "Exploring Remote Resources"
teaching: 25
exercises: 10
questions:
- "How does my local computer compare to the remote systems?"
- "How does the login node compare to the compute nodes?"
- "Are all compute nodes alike?"
objectives:
- "Survey system resources and the queuing system"
- "Compare & contrast resources on the local machine, login node, and worker
nodes"
- "Learn about the various filesystems on the cluster using `df`"
- "Find out `who` else is logged in"
- "Assess the number of idle and occupied nodes"
keypoints:
- "An HPC system is a set of networked machines."
- "HPC systems typically provide login nodes and a set of compute nodes."
- "The resources found on independent (worker) nodes can vary in volume and
  type (amount of RAM, processor architecture, availability of network mounted
  filesystems, etc.)."
- "Files saved on shared storage are available on all nodes."
- "The login node is a shared machine: be considerate of other users."
---

## Look Around the Remote System

If you have not already connected to {{ site.remote.name }}, please do so now:

```
{{ site.local.prompt }}  ssh {{ site.remote.user }}@{{ site.remote.login }}
```
{: .language-bash}

Take a look at your home directory on the remote system:

```
{{ site.remote.prompt }} ls
```
{: .language-bash}

> ## What's different between your machine and the remote?
>
> Open a second terminal window on your local computer and run the `ls` command
> (without logging in to {{ site.remote.name }}). What differences do you see?
>
> > ## Solution
> >
> > You would likely see something more like this:
> >
> > ```
> > {{ site.local.prompt }} ls
> > ```
> > {: .language-bash}
> > ```
> > Applications Documents    Library      Music        Public
> > Desktop      Downloads    Movies       Pictures
> > ```
> > {: .output}
> >
> > The remote computer's home directory shares almost nothing in common with
> > the local computer: they are completely separate systems!
> {: .solution}
{: .discussion}

Most high-performance computing systems run the Linux operating system, which
is built around the UNIX [Filesystem Hierarchy Standard][fshs]. Instead of
having a separate root for each hard drive or storage medium, all files and
devices are anchored to the "root" directory, which is `/`:

```
{{ site.remote.prompt }} ls /
```
{: .language-bash}
```
bin   etc   lib64  proc  sbin     sys  var
boot  {{ site.remote.homedir | replace: "/", "" }}  mnt    root  scratch  tmp  working
dev   lib   opt    run   srv      usr
```
{: .output}

The "{{ site.remote.homedir | replace: "/", "" }}" directory is the one where
we generally want to keep all of our files. Other folders on a UNIX OS contain
system files and change as you install new software or upgrade your OS.

> ## Using HPC filesystems
>
> On HPC systems, you have a number of places where you can store your files.
> These differ in both the amount of space allocated and whether or not they
> are backed up.
>
> * ***Home*** -- often a _network filesystem_, data stored here is available
>   throughout the HPC system, and often backed up periodically. Files stored
>   here are typically slower to access, the data is actually stored on another
>   computer and is being transmitted and made available over the network!
> * ***Scratch*** -- typically faster than the networked Home directory, but not
>   usually backed up, and should not be used for long term storage.
> * ***Data*** -- sometimes provided as an alternative to Scratch space, Data is
>   a fast file system accessed over the network. Typically, this will have
>   higher performance than your home directory, but lower performance than
>   Scratch; it may not be backed up. It differs from Scratch space in that
>   files in a Data file system are not automatically deleted for you: you must
>   manage the space yourself.
>   .
{: .callout}

{% include figure.html url="" max-width="70%"
   file="/fig/filepathgadi.png"
   alt="filepath cluster" caption="" %}

#### For this workshop:

QCIF training Project Number: ***cd82***

QCIF training Institute Number: ***578***


Post workshop, when you are allocated an account on NCI, you can check your Project number and Institute number via [The NCI account portal](https://my.nci.org.au/mancini/login)

## Let's explore some important folders specific to Gadi

For further information, you can see [this Gadi documentation page](https://opus.nci.org.au/spaces/Help/pages/230490763/Gadi+Quick+Reference+Guide...)

### Home

You will usually start in your home folder, it would look like this. The command 'pwd' says 'Print the Working Directory'. Your home will be /home/YourProjectName/YourUsername. On Gadi, you are limited to 10Gb size quota. 

```
{{ site.remote.prompt }} pwd
```
{: .language-bash}
```
/home/578/am9079
```
{: .output}


### Scratch

Scratch is the place for very short term processing. 

Scratch is intended to be used as the main computing space for your compute needs. However, once your jobs have run, please move your data to a different directory. Files left in scratch for 100 days will be quarantined and potentially deleted permanently. You will get 1TB by default and are able to request more. 

```
{{ site.remote.prompt }} ls /scratch/
```
{: .language-bash}
```
cd82    public
```
{: .output}



```
{{ site.remote.prompt }} ls /scratch/cd82/YourUserName/
```
{: .language-bash}
```
tmp
```
{: .output}

There's a folder in here called tmp - often shorthand for temporary. This is a place for us to store short term files for processing.

#### How much space do I have available?

You can check how much quota of storage you have available by using the command lquota.

```
{{ site.remote.prompt }} lquota
```
{: .language-bash}
```
[am9079@gadi-login-09 ~]$ lquota
--------------------------------------------------------------------------
           fs       Usage      Quota      Limit   iUsage   iQuota   iLimit
--------------------------------------------------------------------------
  cd82 scratch   1.36 GiB   1.00 TiB   1.05 TiB    51446   202000   212100
--------------------------------------------------------------------------

```
{: .output}

### Data

Data is the better place for longer term storage. BUT NOTE- this is not backed up. You should still keep a copy of any important files on your institute's research data store. The size limit is set by the scheme manager. 

```
{{ site.remote.prompt }} ls /g/data
```
{: .language-bash}
```
tmp
```
{: .output}





## Nodes

Individual computers that compose a cluster are typically called _nodes_
(although you will also hear people call them _servers_, _computers_ and
_machines_). On a cluster, there are different types of nodes for different
types of tasks. The node where you are right now is called the _login node_,
_head node_, _landing pad_, or _submit node_. A login node serves as an access
point to the cluster.

As a gateway, the login node should not be used for time-consuming or
resource-intensive tasks. You should be alert to this, and check with your
site's operators or documentation for details of what is and isn't allowed. It
is well suited for uploading and downloading files, setting up software, and
running tests. Generally speaking, in these lessons, we will avoid running jobs
on the login node.

Who else is logged in to the login node?

```
{{ site.remote.prompt }} who
```
{: .language-bash}

This may show only your user ID, but there are likely several other people
(including fellow learners) connected right now.

> ## Dedicated Transfer Nodes
>
> If you want to transfer larger amounts of data to or from the cluster,
> GADI offer dedicated nodes for data transfers only. The motivation for
> this lies in the fact that larger data transfers should not obstruct
> operation of the login node for anybody else.  As a
> rule of thumb, consider all transfers of a volume larger than 500 MB to 1 GB
> as large.
> Gadi has 6 six dedicated data-mover nodes that handle
>  moving data to and from the system at a high speed.
> These data-mover nodes have the domain name 'gadi-dm.nci.org.au',
> and you can use this when moving data to and from the system.
> We'll try them out in our transferring files lesson.
{: .callout}

The real work on a cluster gets done by the _compute_ (or _worker_) _nodes_.
compute nodes come in many shapes and sizes, but generally are dedicated to long
or hard tasks that require a lot of computational resources.

All interaction with the compute nodes is handled by a specialized piece of
software called a scheduler (the scheduler used in this lesson is called
{{ site.sched.name }}). We'll learn more about how to use the
scheduler to submit jobs next, but for now, it can also tell us more
information about the compute nodes.

For example, we can view all of the compute nodes by running the command
`{{ site.sched.info }}`.

```
{{ site.remote.prompt }} {{ site.sched.info }}
```
{: .language-bash}

{% include {{ site.snippets }}/cluster/queue-info.snip %}

A lot of the nodes are busy running work for other users: we are not alone
here!

There are also specialized machines used for managing disk storage, user
authentication, and other infrastructure-related tasks. Although we do not
typically logon to or interact with these machines directly, they enable a
number of key features like ensuring our user account and files are available
throughout the HPC system.

## What's in a Node?

All of the nodes in an HPC system have the same components as your own laptop
or desktop: _CPUs_ (sometimes also called _processors_ or _cores_), _memory_
(or _RAM_), and _disk_ space. CPUs are a computer's tool for actually running
programs and calculations. Information about a current task is stored in the
computer's memory. Disk refers to all storage that can be accessed like a file
system. This is generally storage that can hold data permanently, i.e. data is
still there even if the computer has been restarted. While this storage can be
local (a hard drive installed inside of it), it is more common for nodes to
connect to a shared, remote fileserver or cluster of servers.

{% include figure.html url="" max-width="40%"
   file="/fig/node_anatomy.png"
   alt="Node anatomy" caption="" %}


## Let's consider our first example again.....

Let's look at our first examples again.

1. A statistics student wants to cross-validate a model. This involves running
  the model 1000 times -- but each run takes an hour. Running the model on
  a laptop will take over a month!

This student has lots of little boxes - we need multiple delivery trucks so boxes can be delivered independantly. We don't have to wait for box 1 to be delivered to deliver box 2.

In HPC or IT language, what we need here is lots of smaller 'nodes' with average cores (CPU) and (RAM) memory. So we only need small nodes, but we do need lots of them.

Think of nodes as our delivery vehicles, with the cores and memory specifying what type of truck, and how big it is.

Something to consider - if we ask for a node is very large, then there may be quite a lineup in front of us, and we have to wait until it's available. Often, HPC clusters have lots of smaller nodes, but only a few large ones.
A large node and a small node will normally take the same amount of time to run the same calculation- just like how a massive truck and a small delivery van would take the same amount of time to drive to a location. 

So if you don't need the big nodes, your jobs will finish much faster by using smaller nodes.

## How about our second example?

2. A genomics researcher has been using small datasets of sequence data, but soon
   will be receiving a new type of sequencing data that is 10 times as large. It’s
   already challenging to open the datasets on a computer – analyzing these larger
   datasets will probably crash it. In this research problem, the calculations required 
   might be impossible to parallelize, but a computer with more memory would be 
   required to analyze the much larger future data set.

This is when you would want a bigger and/or more specialised node. This genomics researcher doesn't need lots of small delivery vehicles - they need only one or two BIG delivery trucks. Or, in more HPC terms, large memory nodes, where the RAM is much bigger than what you would normally have available on your typical desktop computer.  Now, a HPC may only have a couple of these large memory nodes, so the wait time on these may be longer- but they can do the job where a normal desktop computer couldn't.


> ## Explore Your Computer
>
> Try to find out the number of CPUs and amount of memory available on your
> personal computer.
>
> Note that, if you're logged in to the remote computer cluster, you need to
> log out first. To do so, type `Ctrl+d` or `exit`:
>
> ```
> {{ site.remote.prompt }} exit
> {{ site.local.prompt }}
> ```
> {: .language-bash}
>
> > ## Solution
> >
> > There are several ways to do this. Most operating systems have a graphical
> > system monitor, like the Windows Task Manager. More detailed information
> > can be found on the command line:
> >
> > * Run system utilities
> >   ```
> >   {{ site.local.prompt }} nproc --all
> >   {{ site.local.prompt }} free -m
> >   ```
> >   {: .language-bash}
> >
> > * Read from `/proc`
> >   ```
> >   {{ site.local.prompt }} cat /proc/cpuinfo
> >   {{ site.local.prompt }} cat /proc/meminfo
> >   ```
> >   {: .language-bash}
> >
> > * Run system monitor
> >   ```
> >   {{ site.local.prompt }} htop
> >   ```
> >   {: .language-bash}
> {: .solution}
{: .challenge}

> ## Explore the Login Node
>
> Now compare the resources of your computer with those of the login node.
>
> > ## Solution
> >
> > ```
> > {{ site.local.prompt }} ssh {{ site.remote.user }}@{{ site.remote.login }}
> > {{ site.remote.prompt }} nproc --all
> > {{ site.remote.prompt }} free -m
> > ```
> > {: .language-bash}
> >
> > You can get more information about the processors using `lscpu`,
> > and a lot of detail about the memory by reading the file `/proc/meminfo`:
> >
> > ```
> > {{ site.remote.prompt }} less /proc/meminfo
> > ```
> > {: .language-bash}
> >
> > You can also explore the available filesystems using `df` to show **d**isk
> > **f**ree space. The `-h` flag renders the sizes in a human-friendly format,
> > i.e., GB instead of B. The **t**ype flag `-T` shows what kind of filesystem
> > each resource is.
> >
> > ```
> > {{ site.remote.prompt }} df -Th
> > ```
> > {: .language-bash}
> >
> > > ## Different results from `df`
> > >
> > > * The local filesystems (ext, tmp, xfs, zfs) will depend on whether
> > >   you're on the same login node (or compute node, later on).
> > > * Networked filesystems (beegfs, cifs, gpfs, nfs, pvfs) will be similar
> > >   -- but may include {{ site.remote.user }}, depending on how it
> > >   is [mounted][mount].
> > {: .discussion}
> >
> > > ## Shared Filesystems
> > >
> > > This is an important point to remember: files saved on one node
> > > (computer) are often available everywhere on the cluster!
> > {: .callout}
> {: .solution}
{: .challenge}

> ## Compare Your Computer, the Login Node and the Compute Node
>
> Compare your laptop's number of processors and memory with the numbers you
> see on the cluster login node and compute node. What implications do
> you think the differences might have on running your research work on the
> different systems and nodes?
>
> > ## Solution
> >
> > Compute nodes are usually built with processors that have _higher
> > core-counts_ than the login node or personal computers in order to support
> > highly parallel tasks. Compute nodes usually also have substantially _more
> > memory (RAM)_ installed than a personal computer. More cores tends to help
> > jobs that depend on some work that is easy to perform in _parallel_, and
> > more, faster memory is key for large or _complex numerical tasks_.
> {: .solution}
{: .discussion}

> ## Differences Between Nodes
>
> Many HPC clusters have a variety of nodes optimized for particular workloads.
> Some nodes may have larger amount of memory, or specialized resources such as
> Graphics Processing Units (GPUs or "video cards").
{: .callout}

With all of this in mind, we will now cover how to talk to the cluster's
scheduler, and use it to start running our scripts and programs!

{% include links.md %}

[fshs]: https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard
[mount]: https://en.wikipedia.org/wiki/Mount_(computing)
