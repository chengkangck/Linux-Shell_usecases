# Server system configuration and initialization

1. Set time zone and synchronize time
① Script writing
②Script description
2. Disable selinux
① Script writing
②Script description
3. Turn off the firewall
① Script writing
②Script description
4. History commands show operating time
① Script writing
②Script description
5. Prohibit root remote login
① Script writing
②Script description


## 1. Set time zone and synchronize time
- Script writing
```　　　　　　　　 

ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
if ! crontab -l |grep ntpdate &>/dev/null ; then
    (echo "* 1 * * * ntpdate ntp1.aliyun.com >/dev/null 2>&1";hwclock -w;crontab -l) |crontab 
fi

```

- Script description  
View system information
```　　　　　　　　 

[root@ly-01 ~]# cat /etc/redhat-release 
CentOS Linux release 7.6.1810 (Core)


```
```　　　　　　　　 
#View the current default time zone UTC
[root@ly-01 ~]# date
Mon Feb 24 08:59:26 CST 2020

#Modify time zone to Asia Shanghai time zone
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

#Install Linux system time synchronization service software: ntpdate If it is already installed, skip this step
[root@localhost ~]# yum -y install ntpdate

#LinuxSystem Time Synchronization
# Let the current server synchronize to the network time, so as to update the current server time. The following command means to synchronize the current server time to ntp1.aliyun.com
[root@localhost ~]# ntpdate ntp1.aliyun.com
#Linux Hardware Time Synchronization: Modify the server hardware time to map to our system time. It can also be said that the system time is synchronized to the hardware time to prevent the time from being restored after the system restarts.
hwclock -w

#Timing synchronization, involving timing tasks
#View current user's timed tasks
[root@ly-01 ~]# crontab -l
#Add a timed task to the current user
[root@ly-01 ~]# crontab -e

#Synchronization time rules, sync once a day at 1 o'clock
* 1 * * *

#Append the error to empty >The specific command executed in the previous section
>/dev/null 2>&1

#Judging whether there is a second timed task in the current timed task list, if yes, skip the logic of setting the timed task below
if! crontab -l |grep ntpdate &>/dev/null;
#The following is a set of commands that can be executed directly
(echo "* 1 * * * ntpdate ntp1.aliyun.com >/dev/null 2>&1";hwclock -w;crontab -l) |crontab


```
