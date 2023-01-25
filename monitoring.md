# SAR Linux Sytem Performance Monitoring Tool

SAR stands for **System Activity REport**

By default, it drops a cronjob file for sysstat in the below location and it will run every 10 minutes to collects sar data for historical reference.


## 1. Install `sysstat` in RHEL

```
(base) [root@hpc01 log]# sudo yum install sysstat
Loaded plugins: fastestmirror, langpacks, nvidia, product-id, search-disabled-repos
Determining fastest mirrors
epel/x86_64/metalink                                                                                            |  50 kB  00:00:00     
 * base: mirror.web4africa.co.ke
.....
Running transaction
  Updating   : sysstat-10.1.5-20.el7_9.x86_64                                                                                      1/2 
  Cleanup    : sysstat-10.1.5-19.el7.x86_64                                                                                        2/2 
  Verifying  : sysstat-10.1.5-20.el7_9.x86_64                                                                                      1/2 
  Verifying  : sysstat-10.1.5-19.el7.x86_64                                                                                        2/2 

Updated:
  sysstat.x86_64 0:10.1.5-20.el7_9                                                                                                     

Complete!

```

## 2. Location of the `sar` file in RHEL; `/var/log/sa`

```
(base) [root@hpc01 /]# cd /var/log/sa/
(base) [root@hpc01 sa]# ls -lh
total 0
(base) [root@hpc01 sa]# 

```
## 3. Check the cronjob for `sysstat`

```
(base) [root@hpc01 log]# cat /etc/cron.d/sysstat
# Run system activity accounting tool every 10 minutes
*/10 * * * * root /usr/lib64/sa/sa1 1 1
# 0 * * * * root /usr/lib64/sa/sa1 600 6 &
# Generate a daily summary of process accounting at 23:53
53 23 * * * root /usr/lib64/sa/sa2 -A

```


1. `*/10 * * * *  root /usr/lib64/sa/sa1 1 1` This cron job runs every 10 minutes,and collects 1 report with 1 second interval. It can’t be viewable by a text editor.

2. `53 23 * * * root /usr/lib64/sa/sa2 -A` This cron job runs every day midnight (at 23:53) to create the daily summary report of the sar data. It can be viewable by a text editor. Also, it purges the data older than a particular number of days.


## 4. How to Check the CPU Usage Report of the System Using the sar Command

