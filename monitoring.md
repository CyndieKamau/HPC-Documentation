# SAR Linux Sytem Performance Monitoring Tool

SAR stands for **System Activity REport**

By default, it drops a cronjob file for sysstat in the below location and it will run every 10 minutes to collects sar data for historical reference.

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


