---
title: 'Running FTP on CentOS'
date: 2016-03-11T20:00:00+08:00
draft: false
url: /2016/03/running-ftp-on-centos.html
tags:
- centos
- ftp
cover:
  image: "https://upload.wikimedia.org/wikipedia/commons/b/bf/Centos-logo-light.svg"
  alt: "CentOS"
  relative: false
---

Here's how you can easily setup FTP on CentOS

```
yum install vsftpd
```

Added these to the security group if you are running AWS EC2

Add port range 20-21
Add port range 1024-1048

```
vi /etc/vsftpd/vsftpd.conf
```

```
Set anonymous_enable=NO
Set chroot_local_user=YES
```

Add these

```
pasv_enable=YES
pasv_min_port=1024
pasv_max_port=1048
pasv_address=<Public IP of your instance>
```

```
sudo service vsftpd restart
```

To make sure that the service is started on reboot

```
chkconfig --level 345 vsftpd on
```

Create new FTP User

```
useradd -d /path/to/new/home/dir -G apache userNameHere
chown userNameHere:apache /path/to/new/home/dir
passwd userNameHere
```

The log file can be found in /var/log/xferlog