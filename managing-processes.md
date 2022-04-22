---
title: Managing processes
layout: default
---


A process is a program that is being executed. Processes have the
following attributes:

-   A lifetime.
-   A process ID (PID).
-   A user ID (UID).
-   A group ID (GID).
-   A parent process.
-   An environment.
-   A current working directory.

# 1 Monitoring

Examining subprocesses of your shell with `ps`:

    $ ps
    PID TTY          TIME CMD
    19370 pts/3    00:00:00 bash
    22846 pts/3    00:00:00 ps

Examining in more detail subprocesses of your shell with `ps`:

    $ ps -f
    UID        PID  PPID  C STIME TTY          TIME CMD
    jarrod   19370 19368  0 10:51 pts/3    00:00:00 bash
    jarrod   22850 19370  0 14:57 pts/3    00:00:00 ps -f

Examining in more detail all processes on your computer:

    $ ps -ef
    UID        PID  PPID  C STIME TTY          TIME CMD
    root         1     0  0 Aug21 ?        00:00:05 /usr/lib/systemd
    root         2     0  0 Aug21 ?        00:00:00 [kthreadd]
    root         3     2  0 Aug21 ?        00:00:07 [ksoftirqd/0]
    root         5     2  0 Aug21 ?        00:00:00 [kworker/0:0H]
       <snip>
    root     16210     1  0 07:19 ?        00:00:00 login -- jarrod
    jarrod   16219 16210  0 07:19 tty1     00:00:00 -bash
    jarrod   16361 16219  0 07:19 tty1     00:00:00 /bin/sh /bin/startx
       <snip>

You can use the `-u` option to see percent CPU and percent memory used
by each process. You can use the `-o` option to provide your own
user-defined format; for example, :

    $ ps -o pid,ni,pcpu,pmem,user,comm
      PID  NI %CPU %MEM USER     COMMAND
    18124   0  0.0  0.0 jarrod   bash
    22963   0  0.0  0.0 jarrod   ps

To see the hierarchical process structure (i.e., which processes started which other processes), you can use the `pstree`
command.

Examining processes with `top`:

    $ top
    top - 13:49:07 up  1:49,  3 users,  load average: 0.10, 0.15, 0.18
    Tasks: 160 total,   1 running, 158 sleeping,   1 stopped,   0 zombie
    %Cpu(s):  2.5 us, 0.5 sy, 0.0 ni, 96.9 id, 0.0 wa, 0.0 hi, 0.0 si, 0.0 st
    KiB Mem : 7893644 total, 5951552 free, 1085584 used,  856508 buff/cache
    KiB Swap: 7897084 total, 7897084 free,       0 used. 6561548 avail Mem 

      PID USER     PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND 
     1607 jarrod   20   0 2333568 974888 212944 S  12.5 12.4  11:10.67 firefox
     3366 jarrod   20   0  159828   4312   3624 R   6.2  0.1   0:00.01 top
        1 root     20   0  193892   8484   5636 S   0.0  0.1   0:01.78 systemd 

The `RES` column indicates the amount of memory that a process is using (in bytes, if not otherwise indicated), while the `%MEM` shows that memory use relative to the physical memory availabe on the computer.

To quit `top`, type `q`.

# 2 Signaling

Let's see how to build up a command to kill firefox using some of the
tools we've seen. First let's pipe the output of `ps -e` to `grep` to
select the line corresponding to `firefox`:

    $ ps -e | grep firefox
    16517 ?        00:10:03 firefox

We can now use `awk` to select the first column, which contains the
process ID corresponding to `firefox`:

    $ ps -e | grep firefox | awk '{ print $1 }'
    16517

Finally, we can pipe this to the `kill` command using `xargs`:

    $ ps -e | grep firefox | awk '{ print $1 }' | xargs kill

# 3 Job Control

When you run a command in a shell by simply typing its name, you are
said to be running in the foreground. When a job is running in the
foreground, you can't type additional commands into that shell session,
but there are two signals that can be sent to the running job through
the keyboard. To interrupt a program running in the foreground, use
`Ctrl-c`; to quit a program, use `Ctrl-\`. While modern windowed systems
have lessened the inconvenience of tying up a shell with foreground
processes, there are some situations where running in the foreground is
not adequate.

The primary need for an alternative to foreground processing arises when
you wish to have jobs continue to run after you log off the computer. In
cases like this you can run a program in the background by simply
terminating the command with an ampersand (`&`). However, before putting
a job in the background, you should consider how you will access its
results, since *stdout* is not preserved when you log off from the
computer. Thus, redirection (including redirection of *stderr*) is
essential when running jobs in the background. As a simple example,
suppose that you wish to run an R script, and you don't want it to
terminate when you log off. (Note that this can also be done using
`R CMD BATCH`, so this is primarily an illustration.)

    $ R --no-save < code.R > code.Rout 2>&1 &
    
What does the inscrutable `2>&1` do? It says to send *stderr* to the same
place as *stdout*, which in this case has been redirected to `code.Rout`.

If you forget to put a job in the background when you first execute it,
you can do it while it's running in the foreground in two steps. First,
suspend the job using the `Ctrl-z` signal. After receiving the signal,
the program will interrupt execution, but will still have access to all
files and other resources. Next, issue the `bg` command, which will
start the stopped job running in the background.

## 3.1 Listing and killing jobs

Since only foreground jobs will accept signals through the keyboard, if
you want to terminate a background job you must first determine the
unique process id (PID) for the process you wish to terminate through
the use of the `ps` command.

To see all processes owned by a specific user (e.g., `jarrod`), I can
use the `-U jarrod` option:

    $ ps -U jarrod

If I want to get more information (e.g., `%CPU` and `%MEM`), I can use
add the `-u` option:

    $ ps -U jarrod -u
    USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START    TIME COMMAND
    jarrod   16116 12.0  6.0 118804  5080 tty1     Ss   16:25  133:01 python

In this example, the `ps` output tells us that this python job has a PID
of `16116`, that it has been running for 133 minutes, is using 12% of
CPU and 6% of memory, and that it started at 16:25. You could then issue
the command:

    $ kill 11998

or, if that doesn't work:

    $ kill -9 11998

to terminate the job. Another useful command in this regard is
`killall`, which accepts a program name instead of a process id, and
will kill all instances of the named program:

    $ killall R

Of course, it will only kill the jobs that belong to you, so it will not
affect the jobs of other users. Note that the `ps` and `kill` commands
only apply to the particular computer on which they are executed, not to
the entire computer network. Thus, if you start a job on one machine,
you must log back into that same machine in order to manage your job.

## 3.2 Monitoring jobs and memory use

As we saw above, the `top` command also allows you to monitor the jobs
on the system and in real-time. In particular, it's useful for seeing
how much of the CPU and how much memory is being used, as well as
figuring out a PID as an alternative to `ps`. You can also renice jobs
(see below) and kill jobs from within top: just type `r` or `k`,
respectively, and proceed from there.

One of the main things to watch out for is a job that is using close to
100% of memory and much less than 100% of CPU. What is generally
happening is that your program has run out of memory and is using
virtual memory on disk, spending most of its time writing to/from disk,
sometimes called *paging* or *swapping*. If this happens, it can be a
very long time, if ever, before your job finishes.

Note that the per-process memory use reported by `top` and `ps` may "double count"
memory that is being used simultaneously by multiple processes. To see the total
amount of memory actually available on a machine:

```
free -h
```

```
              total        used        free      shared  buff/cache   available
Mem:           251G        998M        221G        2.6G         29G        247G
Swap:          7.6G        210M        7.4G
```

You'll generally be interested in the `Memory` row and in the `total`, `used` and `available` columns.
The `free` column can be confusing and does not actually indicate how much memory is still available
to be used, as discused [here](https://berkeley-scf.github.io/tutorial-databases/db-management#52-memory).

## 3.3 Nicing a job

The most important thing to remember when starting a job on a machine
that is not your personal machine is how to be a good citizen. This
often involves 'nicing' your jobs. Nicing a job puts it at a lower
priority so that a user working at the keyboard has higher priority in
using the CPU. Here's how to do it, giving the job a low priority of 19:

    $ nice -19 R CMD BATCH --no-save code.R code.Rout &

If you forget and just submit the job without nicing, you can reduce the
priority by doing:

    $ renice +19 11998

where `11998` is the PID of your job.

## 3.4 Screen

Screen allows you to create virtual terminals, which are not connected
to your actual terminal or shell. This allows you to run multiple
programs from the commandline and leave them all in the foreground in
their own virtual terminal. Screen provides facilities for managing
several virtual terminals including listing them, switching between
them, disconnecting from one machine and then reconnecting from another.
While we will only discuss its basic operation, we will cover enough to
be of regular use.

Calling screen :

    $ screen

will open a single window and you will see a new bash prompt. You just
work at this prompt as you normally would. The difference is that you
can disconnect from this window by typing `Ctrl-a d` and you will see
something like this :

    $ screen
    [detached from 23974.pts-2.t430u]

> **tip**

> All the screen key commands begin with the control key combination
> `Ctrl-a` followed by another key. For instance, when you are in a
> screen session and type `Ctrl-a ?`, screen will display a help screen
> with a list of its keybindings.

You can now list your screen sessions :

    $ screen -ls 
    There is a screen on:
            23974.pts-2.t430u       (Detached)

To reconnect :

    $ screen -r

You can start multiple screen sessions. This is what it might look like
if you have 3 screen sessions:

    $ screen -ls 
    There are screens on:
            24274.pts-2.t430u       (Attached)
            24216.pts-2.t430u       (Detached)
            24158.pts-2.t430u       (Detached)

To specify that you want to reattach to session `24158.pts-2.t430u`,
type:

    $ screen -r 24158.pts-2.t430u

If you have several screen sessions, you will want to name your screen
session something more informative than `24158.pts-2.t430u`. To name a
screen session `gene-analysis` you can use the `-S` option when calling
screen:

    $ screen -S gene-analysis

While there are many more features and keybindings available for screen,
you've already seen enough screen to be useful. For example, imagine you
ssh to a remote machine from your laptop to run an analysis. The first
thing you do at the bash prompt on the remote machine is:

    $ screen -S dbox-study

Then you start your analysis script `dbox-analysis.py` running:

    $ dbox-analysis.py
    Starting statistical analysis ...
    Processing subject 1 ...
    Processing subject 2 ...

If your study has 50 subjects and processing each subject takes 20
minutes, you will not want to sit there watching your monitor. So you
use `Ctrl-a d` to detach the session and you will then see:

    $ screen -S dbox-study
    [detached from 2799.dbox-study]
    $

Now you can log off your laptop and go home. Sometime after dinner, you
decide to check on your job. So you ssh from your home computer to the
remote machine again and type the following at the bash prompt:

    $ screen -r dbox-study
