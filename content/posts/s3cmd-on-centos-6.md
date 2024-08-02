---
title: 'S3cmd on CentOS 6'
date: 2016-03-30T12:00:00.000+08:00
draft: false
url: /2016/03/s3cmd-on-centos-6.html
tags:
- centos
- s3cmd
- amazon s3
cover:
  image: "https://upload.wikimedia.org/wikipedia/commons/b/bf/Centos-logo-light.svg"
  alt: "CentOS"
  relative: false
---

S3cmd is a command line tool that we've used for uploading, retrieving and managing data in Amazon S3.

To install S3cmd, you would need to download the .repo for the tool, and then, run install.

```
cd /etc/yum.repos.d
wget http://s3tools.org/repo/RHEL_6/s3tools.repo
yum install s3cmd
```

Answer yes when asked to accept a new GPG key.

Note: The latest version of s3cmd requires python 2.6. If you come across this error Problem: ImportError: No module named S3.Exceptions, chances are, you do not have the correct version. One work around is as follows:-

```
vi /usr/bin/s3cmd
```

Add 2.6 to the first line, so it looks like:

```
#!/usr/bin/python2.6
```