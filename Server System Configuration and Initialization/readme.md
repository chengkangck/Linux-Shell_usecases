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
