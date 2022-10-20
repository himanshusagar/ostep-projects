
# An xv6 Priority Scheduler

In this project, you'll be putting a new scheduler into xv6. It is called a
**simple priority scheduler**.  The basic idea is simple: if a process sets its
ticket value to 1 it has high priority; otherwise,
a process should have a ticket value of 0 and thus have low priority.
Priority can only be 0 or 1. All other ticket values are not valid;
1 (high priority) should be the default.
High-priority processes should always have priority over low-priority processes,
e.g., if there is a single high-priority process and a single low-priority process,
the high-priority one will run to completion
(thus starving the low-priority process).
If there are two or more high-priority processes,
they should alternate round-robin style;
similarly, if there are no high-priority processes and
more than one low-priority processes,
they should alternate round-robin.

The objectives for this project:
* To gain further knowledge of a real kernel, xv6.
* To familiarize yourself with a scheduler.
* To change that scheduler to a new algorithm.
* To make a graph to show your project behaves appropriately.


## Details

You'll need two new system calls to implement this scheduler. The first is
`int setpriority(int number)`, which set the priority/ticket of the calling
process. By default, each process should get 1 as priority(high priority); This routine can be used to
set priority as 0 or 1. This routine should return 0 if successful, and -1 otherwise (if, for example, the caller passes in a
number less than one).

The second is `int getpinfo(struct pstat *)`. This routine returns some
information about all running processes, including how many times each has
been chosen to run and the process ID of each. You can use this system call to
build a variant of the command line program `ps`, which can then be called to
see what is going on. The structure `pstat` is defined below; note, you cannot
change this structure, and must use it exactly as is. This routine should
return 0 if successful, and -1 otherwise (if, for example, a bad or NULL
pointer is passed into the kernel).

Most of the code for the scheduler is quite localized and can be found in
`proc.c`; the associated header file, `proc.h` is also quite useful to
examine. To change the scheduler, not much needs to be done; study its control
flow and then try some small changes.

You'll need to assign priority to a process when it is created. Specifically,
you'll need to make sure a child process *inherits* the same priority
as its parent. Thus, if the parent has priority 0, and calls **fork()** to
create a child process, the child should also get 0 priority.

Finally, you'll need to understand how to fill in the structure `pstat` in the
kernel and pass the results to user space. The structure should look like what
you see here, in a file you'll have to include a file called `pstat.h`:

```c
#ifndef _PSTAT_H_
#define _PSTAT_H_

#include "param.h"

struct pstat {
  int inuse[NPROC];   // whether this slot of the process table is in use (1 or 0)
  int priority[NPROC]; // priority of process 
  int pid[NPROC];     // the PID of each process 
  int ticks[NPROC];   // the number of ticks each process has accumulated 
};

#endif // _PSTAT_H_
```

Good examples of how to pass arguments into the kernel are found in existing
system calls. In particular, follow the path of `read()`, which will lead you
to `sys_read()`, which will show you how to use `argptr()` (and related calls)
to obtain a pointer that has been passed into the kernel. Note how careful the
kernel is with pointers passed from user space -- they are a security
threat(!), and thus must be checked very carefully before usage.


## Graph

Beyond the usual code, you'll have to make a graph for this assignment. The
graph should show the number of time slices(ticks) a set of 4 processes receives
over time, where two processes have priory 1 and two have priority 0.(e.g., process A
might have priority 1, process B 1, and process C 0 and process D 0). The graph is likely to
be pretty boring, but should clearly show that your priority scheduler works as
desired. 



