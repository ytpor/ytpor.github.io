---
title: 'csync2 Installation and Setup'
date: 2016-03-14T12:00:00+08:00
draft: false
url: /2016/03/csync2-installation-and-setup.html
tags:
- csync2
---

Csync2 is a cluster synchronization tool. It can be used to keep files on multiple hosts in a cluster in sync. Csync2 can handle complex setups with much more than just 2 hosts, handle file deletions and can detect conflicts.

### Installation

Build and Install csycn2 by running the below script - build-csync2.sh

```
#!/bin/sh
# build-csync2.sh

# Make directory to store csync2 related
mkdir -p /data/build/
mkdir -p /data/sync-db/
mkdir -p /data/logs/csync2
mkdir -p /data/sync-conflicts/

cd /data/build/

# Get the files from our own file repository
wget http://downloads.sourceforge.net/librsync/librsync-0.9.7.tar.gz
wget http://oss.linbit.com/csync2/csync2-2.0.tar.gz

# Install the packages required to compile csync2
yum install xinetd byacc flex gcc-c++ gnutls gnutls-devel openssl-devel openssl-static sqlite-devel -y

# Untar the files
tar -xzf librsync-0.9.7.tar.gz
tar -xzf csync2-2.0.tar.gz

# Build csync2
cd /data/build/csync2-2.0
./configure \
    --prefix=/usr \
    --with-librsync-source=/data/build/librsync-0.9.7.tar.gz \
    --localstatedir=/var \
    --sysconfdir=/etc \
    --disable-gnutls
make && make install

# Remove the directories that we no longer need
cd ..
rm -rf librsync-0.9.7
rm -rf csync2-2.0

# Add the csync2 port number to /etc/services
echo "csync2        30865/tcp" >> /etc/services

CSYNCLOC=`which csync2`

# Create a xinted definition file for csync2
echo -e "# default: on\n# description: csync2 xinetd server\n\nservice csync2\n{\n       disable         = no\n       flags           = REUSE\n       socket_type     = stream\n       wait            = no\n       user            = root\n       group           = root\n       server          = $CSYNCLOC\n       server_args     = -i -D /data/sync-db/\n       port            = 30865\n       type            = UNLISTED\n       log_type        = FILE /data/logs/csync2/csync2-xinetd.log\n       log_on_failure  += USERID\n}\n" > /etc/xinetd.d/csync2

# Restart the service to include the newly created definition
service xinetd restart
```

### Setup

First, generate ONE self-signed SSL certificate that would be used by your cluster.

```
openssl genrsa -out /etc/csync2_ssl_key.pem 1024
openssl req -new -key /etc/csync2_ssl_key.pem -out /etc/csync2_ssl_cert.csr
```

Answer these questions.

```
Country Name (2 letter code) [XX]:CY
State or Province Name (full name) []:CY
Locality Name (eg, city) [Default City]:CY
Organization Name (eg, company) [Default Company Ltd]:Acme Limited
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:Acme Limited
```

```
openssl x509 -req -days 600 -in /etc/csync2_ssl_cert.csr -signkey /etc/csync2_ssl_key.pem -out /etc/csync2_ssl_cert.pem
```

Create the csync2 key file

```
csync2 -k /etc/csync2-production.key

```

### Sample configuration

You can find the the configuration file at /etc/csync2.cfg. All servers in this cluster MUST have the same configuration.

```
nossl * *;
group test {
    host ip-10-0-0-99;
    host (ip-10-0-1-119);
    key /etc/csync2-production.key;

    include /var/www/html;
    exclude /var/www/html/configuration;

    auto none;
}
```

### Sample hosts file

The hosts file can be found at /etc/hosts

```
54.72.31.23 ip-10-0-0-99
52.91.3.112 ip-10-0-1-119
```

Note: ip-10-0-0-99 can be found by running hostname on the server. This must be used.

### Sample cron entry

```
*/5 * * * * /usr/sbin/csync2 -x -v -D /data/sync-db/ >/dev/null 2>&1
```

### References

*   [LINBIT Open Source Software - csync2](http://oss.linbit.com/csync2/)
*   [Synchronising files amongst your cloud nodes with Csync2 - It’s as easy as 1,2,3](https://www.itoc.com.au/blog/synchronising-files-amongst-your-cloud-nodes-with-csync2-its-as-easy-as-123)