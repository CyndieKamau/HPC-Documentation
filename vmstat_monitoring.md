# VMSTAT: Virtual Memory Statistics Reporter:

 vmstat reports information about processes, memory, paging, block IO, traps, disks and cpu activity.

**N.B** When you run vmstat, keep in mind that the first report is an average of the requested information since the time of the last reboot. Subsequent reports use measurements of delay and count. I cover those specifically during the syntax discussion.

## **FIELD DESCRIPTION FOR VM MODE**

   **Procs**
   
       r: The number of runnable processes (running or waiting for run time).
       b: Number of processes blocked waiting for I/O to complete.

   **Memory**
   
       swpd: the amount of virtual memory used.
       free: the amount of idle memory.
       buff: the amount of memory used as buffers.
       cache: the amount of memory used as cache.
       inact: the amount of inactive memory.  (-a option)
       active: the amount of active memory.  (-a option)

   **Swap**
   
       si: Amount of memory swapped in from disk (/s).
       so: Amount of memory swapped to disk (/s).

   **IO**
   
       bi: Blocks received from a block device (blocks/s).
       bo: Blocks sent to a block device (blocks/s).
       
   **System**
   
       in: The number of interrupts per second, including the clock.
       cs: The number of context switches per second.

   **CPU**
   
       These are percentages of total CPU time.
       us: Time spent running non-kernel code.  (user time, including nice time)
       sy: Time spent running kernel code.  (system time)
       id: Time spent idle.  Prior to Linux 2.5.41, this includes IO-wait time.
       wa: Time spent waiting for IO.  Prior to Linux 2.5.41, included in idle.
       st: Time stolen from a virtual machine.  Prior to Linux 2.6.11, unknown.

    
## 1. Command Syntax `vmstat`:

```
(base) [root@hpc01 /]# vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 5  0 3936580 195379248 400272 41265860    0    0     9    44    0    0  3  3 94  0  0

```
From the statistics here:

* `r` There are 5 runnable processes
* `b` No blocked processes
* `swpd` 3936530 virtual memory used
* `free` 195379248 free memory left 
etc.

## 2. `vmstat -s`

Displays various memory statistics as well as CPU and IO event counters:

```
(base) [root@hpc01 /]# vmstat -s
    263943312 K total memory
     26906740 K used memory
     52896424 K active memory
      8996332 K inactive memory
    192073856 K free memory
       400272 K buffer memory
     44562444 K swap cache
      4194300 K total swap
      3936580 K used swap
       257720 K free swap
   1181181077 non-nice user cpu ticks
       140378 nice user cpu ticks
   1111378603 system cpu ticks
  39368318856 idle cpu ticks
     16429308 IO-wait cpu ticks
            0 IRQ cpu ticks
    296942970 softirq cpu ticks
            0 stolen cpu ticks
   3633964529 pages paged in
  18578791218 pages paged out
      8189912 pages swapped in
     15185041 pages swapped out
   3126473045 interrupts
   1481849462 CPU context switches
   1668077569 boot time
     21803810 forks

```
