---
title: "Connecting to a remote HPC system"
teaching: 25
exercises: 10
questions:
- "How do I log in to a remote HPC system?"
objectives:
- "Configure secure access to a remote HPC system."
- "Connect to a remote HPC system."
keypoints:
- "An HPC system is a set of networked machines."
- "HPC systems typically provide login nodes and a set of worker nodes."
- "The resources found on independent (worker) nodes can vary in volume and
  type (amount of RAM, processor architecture, availability of network mounted
  filesystems, etc.)."
- "Files saved on one node are available on all nodes."
---

## Secure Connections

The first step in using a cluster is to establish a connection from our laptop
to the cluster. When we are sitting at a computer (or standing, or holding it
in our hands or on our wrists), we have come to expect a visual display with
icons, widgets, and perhaps some windows or applications: a _graphical user
interface_, or GUI. Since computer clusters are remote resources that we
connect to over slow or intermittent interfaces (WiFi and VPNs especially), it
is more practical to use a _command-line interface_, or CLI, to send commands
as plain-text. If a command returns output, it is printed as plain text as
well. The commands we run today will not open a window to show graphical
results.

If you have ever opened the Windows Command Prompt or macOS Terminal, you have
seen a CLI. If you have already taken The Carpentries' courses on the UNIX
Shell or Version Control, you have used the CLI on your _local machine_
extensively. The only leap to be made here is to open a CLI on a _remote machine_,
while taking some precautions so that other folks on the network can't see (or
change) the commands you're running or the results the remote machine sends
back. We will use the Secure SHell protocol (or SSH) to open an encrypted
network connection between two machines, allowing you to send & receive text
and data without having to worry about prying eyes.

{% include figure.html url="" max-width="50%"
   file="/fig/connect-to-remote.svg"
   alt="Connect to cluster" caption="" %}

SSH clients are usually command-line tools, where you provide the remote
machine address as the only required argument. If your username on the remote
system differs from what you use locally, you must provide that as well. If
your SSH client has a graphical front-end, such as PuTTY or MobaXterm, you will
set these arguments before clicking "connect." From the terminal, you'll write
something like `ssh userName@hostname`, where the argument is just like an
email address: the "@" symbol is used to separate the personal ID from the
address of the remote machine.


## Log In to the Cluster

Go ahead and open your terminal or graphical SSH client, then log in to the
cluster. Replace `{{ site.remote.user }}` with your username or the one
supplied by the instructors.

```
{{ site.local.prompt }} ssh {{ site.remote.user }}@{{ site.remote.login }}
```
{: .language-bash}


## What you'll see as a response

When you first log in, it will give you a prompt similar to below:

```
The authenticity of host 'gadi.nci.org.au (203.0.19.85)' can't be established.
ED25519 key fingerprint is SHA256:abunchofnumbersandletterslkajsdlkfjalfj.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? 
```


Type in 'yes'


You may be asked for your password. Watch out: the characters you type after
the password prompt are not displayed on the screen. Normal output will resume
once you press `Enter`.


```
{{ site.remote.user }}@{{ site.remote.login }}'s password: 
###############################################################################
#                  Welcome to the NCI National Facility!                      #
#      This service is for authorised clients only. It is a criminal          #
#      offence to:                                                            #
#                - Obtain access to data without permission                   #
#                - Damage, delete, alter or insert data without permission    #
#      Use of this system requires acceptance of the Conditions of Use        #
#      published at http://nci.org.au/users/nci-terms-and-conditions-access   #
###############################################################################
|         gadi.nci.org.au - 260,760 processor InfiniBand x86_64 cluster       | 
===============================================================================
{{ site.remote.prompt }}

```


This means you have logged on successfully.

You may have noticed that the prompt changed when you logged into the remote
system using the terminal (if you logged in using PuTTY this will not apply
because it does not offer a local terminal). This change is important because
it can help you distinguish on which system the commands you type will be run
when you pass them into the terminal. This change is also a small complication
that we will need to navigate throughout the workshop. Exactly what is displayed
as the prompt (which conventionally ends in `$`) in the terminal when it is
connected to the local system and the remote system will typically be different
for every user. We still need to indicate which system we are entering commands
on though so we will adopt the following convention:

* `{{ site.local.prompt }}` when the command is to be entered on a terminal
  connected to your local computer
* `{{ site.remote.prompt }}` when the command is to be entered on a
  terminal connected to the remote system
* `$` when it really doesn't matter which system the terminal is connected to.

### Different number in prompt?

You may see a different number in the prompt [yourUsername@gadi-login-__04__~]$ instead of 04. That's perfectly fine. 

## A different way to log in - Using Australia Research Environment

NCI also have available a way to log in through a website called Australia Research 
Environment [https://are.nci.org.au](https://are.nci.org.au). If you are having issues accessing 
login via ssh, this can be used for today's workshop. 

Log in with your NCI username (not email) and password, and click 'Gadi Terminal'.


{% include figure.html url="" max-width="80%"
   file="/fig/GADI.ARI.png"
   alt="Screenshot of ARI" caption="" %}


## Looking Around Your Remote Home

Very often, many users are tempted to think of a high-performance computing
installation as one giant, magical machine. Sometimes, people will assume that
the computer they've logged onto is the entire computing cluster. So what's
really happening? What computer have we logged on to? The name of the current
computer we are logged onto can be checked with the `hostname` command. (You
may also notice that the current hostname is also part of our prompt!)

```
{{ site.remote.prompt }} hostname
```
{: .language-bash}

```
{{ site.remote.host }}
```
{: .output}

So, we're definitely on the remote machine. Next, let's find out where we are
by running `pwd` to print the working directory.

```
{{ site.remote.prompt }} pwd
```
{: .language-bash}

```
{{ site.remote.homedir }}/{{ site.remote.user }}
```
{: .output}

Great, we know where we are! Let's see what's in our current directory:

```
{{ site.remote.prompt }} ls
```
{: .language-bash}
```
ondemand
```
{: .output}

The system administrators may have configured your home directory with some
helpful files, folders, and links (shortcuts) to space reserved for you on
other filesystems. If they did not, your home directory may appear empty. To
double-check, include hidden files in your directory listing:

```
{{ site.remote.prompt }} ls -a
```
{: .language-bash}
```
  .            ..    .bash_profile    .bashrc      .config     ondemand    .ssh
```
{: .output}

In the first column, `.` is a reference to the current directory and `..` a
reference to its parent (`{{ site.remote.homedir }}`). You may or may not see
the other files, or files like them: `.bashrc` is a shell configuration file,
which you can edit with your preferences; and `.ssh` is a directory storing SSH
keys and a record of authorized connections.

{% unless site.remote.portal %}


```
{{ site.remote.prompt }} logout

Connection to gadi.nci.org.au closed.
```
{: .language-bash}

```
{{ site.local.prompt }} ssh {{ site.remote.user }}@{{ site.remote.login }}
```
{: .language-bash}
{% endunless %}

{% include links.md %}

[bitwarden]: https://bitwarden.com
[fshs]: https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard
[gh-ssh]: https://docs.github.com/en/authentication/connecting-to-github-with-ssh
[keepass]: https://keepass.info
[putty-gen]: https://tartarus.org/~simon/putty-prerel-snapshots/htmldoc/Chapter8.html#pubkey-puttygen
[putty-agent]: https://tartarus.org/~simon/putty-prerel-snapshots/htmldoc/Chapter9.html#pageant
[ssh-agent]: https://www.ssh.com/academy/ssh/agent
[ssh-flags]: https://stribika.github.io/2015/01/04/secure-secure-shell.html
[wiki-rsa]: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
[wiki-dsa]: https://en.wikipedia.org/wiki/EdDSA
