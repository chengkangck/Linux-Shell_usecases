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
## 2. Disable selinux

- Script writing
```　　　　　　　　 

sed -i '/SELINUX/{s/permissive/disabled/}' /etc/selinux/config

```
- Script description
```　　　　　　　　 

#Stream Editor
sed
#Write or modify
-i
#Division Keyword
/SELINUX
#Replace the value permissive in the /etc/selinux/config file with disabled
/permissive/disabled/ /etc/selinux/config

#We manually edit and modify the same
vim /etc/selinux/config
#Replacement value permissive is disabled
```

## 3. Turn off the firewall

- Script writing


```
if egrep "7.[0-9]" /etc/redhat-release &>/dev/null; then
    systemctl stop firewalld
    systemctl disable firewalld
elif egrep "6.[0-9]" /etc/redhat-release &>/dev/null; then
    service iptables stop
    chkconfig iptables off
fi
```　　

- Script description
```
This is a centos7 and centos6 2 version firewall script
First check the system version content in the /etc/redhat-release file, then use regular expressions to distinguish between 7.x or 6.x, then use the pipeline command to filter, and finally, take different branches for different systems
fi
```

## 4. History commands show operating time

What command did the current user execute and when

- Script writing
```
if ! grep HISTTIMEFORMAT /etc/bashrc; then
    echo 'export HISTTIMEFORMAT="%F %T `whoami` "' >> /etc/bashrc
fi

```
- Script description
```
#export HISTTIMEFORMAT Export this variable to the environment variable
#%F %T Date and time format
#whoami Current user
export HISTTIMEFORMAT="%F %T `whoami` "

```

## 5. Prohibit root remote login

- Script writing
```
#sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config


```
- Script description
```
This setting must be cautious: before executing this setting, the administrative user must be created in advance, otherwise, the server cannot be logged in. It is recommended to comment this configuration first and change it to yes root to log in.
Edit the /etc/ssh/sshd_config file and remove the #PermitRootLogin yes comment

```

## 6. Prohibit scheduled tasks to send emails



- Script writing  

When an operation error occurs, the server will send mail regularly. The directory is under /var/mail/, which will increase the number of small files and affect the performance of the server. The above append error is empty, which is also for this purpose.


```
sed -i 's/^MAILTO=root/MAILTO=""/' /etc/crontab

```
- Script description
```
#Edit the /etc/crontab file
vim /etc/crontab
#Replacement format
's/^
#Replaced target
MAILTO=root
#What is the value after replacement
MAILTO=""

```

## 7. Set the maximum number of open files


- Script writing
```
if ! grep "* soft nofile 65535" /etc/security/limits.conf &>/dev/null; then
cat >> /etc/security/limits.conf << EOF
    * soft nofile 65535
    * hard nofile 65535
EOF
fi

```
- Script description
```
#* The scope of the domain, * means all
#soft type There are two kinds of soft and hard
#nofile Indicates the maximum number of open files
#65535 Maximum number of files

* soft nofile 65535

#In simple terms
#Just edit the file /etc/security/limits.conf to append these 2 lines

```

## 8. Less Swap use

- Script writing
```
echo "0" > /proc/sys/vm/swappiness
```
- Script description
```
Edit the /proc/sys/vm/swappiness file to modify the value

```

## 9. System kernel parameter optimization


- Script writing
```
cat >> /etc/sysctl.conf << EOF
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_tw_buckets = 20480
net.ipv4.tcp_max_syn_backlog = 20480
net.core.netdev_max_backlog = 262144
net.ipv4.tcp_fin_timeout = 20  
EOF


```
- Script description
```
Edit the /etc/sysctl.conf file and append the following parameters

#A protection mechanism strategy when tcp overflows
net.ipv4.tcp_syncookies = 1
#Maximum queue length
net.ipv4.tcp_max_tw_buckets = 20480
#Maximum column length for receiving syn
net.ipv4.tcp_max_syn_backlog = 20480
#The maximum queue length of the network card
net.core.netdev_max_backlog = 262144
#Timeout settings
net.ipv4.tcp_fin_timeout = 20

```
```
#View system default parameters
[root@ly-01 mail]# sysctl -a |grep syn_ba

```
## 10. Install system performance analysis tools and others

- Script writing
```
yum install gcc make autoconf vim sysstat wget unzip  net-tools iostat iftop iotp lrzsz -y


```
- Script description
```
Insert code snippet here

```

## 11. SSH timeout
Used for the timeout period after our ssh login: you log in to the server through ssh, and you have not done any operation for a certain period of time
Setting the timeout time is safe

- Script writing

```
if ! grep "TMOUT=600" /etc/profile &>/dev/null; then
    echo "export TMOUT=600" >> /etc/profile
fi


```
- Script description

```
#Append this variable to the configuration file of system environment variables
echo "export TMOUT=600" >> /etc/profile

```
