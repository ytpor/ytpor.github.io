---
title: 'SVN Users and Repositories'
date: 2016-03-07T12:00:00+08:00
draft: false
url: /2016/03/svn-users-and-repositories.html
tags:
- svn
cover:
  image: "https://upload.wikimedia.org/wikipedia/commons/2/22/Apache_Subversion_logo.svg"
  alt: "SVN"
  relative: false
---

We have setup our SVN to be accessed through http with the help of Apache.
Here's a sample configuration that we have used.

```
vi /etc/httpd/conf.d/vhosts.conf
```

Add the below into subversion.conf

```
<VirtualHost *:443>
  ServerName svn.mydomain.com
  DocumentRoot /var/www/svn.mydomain.com

  <Directory /var/www/svn.mydomain.com>
    AllowOverride All
    Order Allow,Deny
    Allow from all
    Options -Indexes
    Require all granted
  </Directory>

  <Location /repos/newrepository>
    DAV svn
    SVNPath /var/svn/repos/newrepository
    AuthName "Subversion repository"
    AuthType Digest
    AuthUserFile /var/svn/svn-auth.htdigest
    AuthzSVNAccessFile /var/svn/svn-acl.conf
    Require valid-user
  </Location>

  SSLEngine on
  SSLProtocol all
  SSLCertificateFile /etc/pki/tls/certs/ca.svn.mydomain.com.crt
  SSLCertificateKeyFile /etc/pki/tls/private/ca.svn.mydomain.com.key

  ErrorLog /var/log/httpd/svn.mydomain.com-error_log
  CustomLog /var/log/httpd/svn.mydomain.com-access_log combined
</VirtualHost>
```

You can create a new SVN user by using the command below. This would store the encrypted password into svn-auth.htdigest

Use this if the file does not exist.

```
htdigest -c /var/svn/svn-auth.htdigest "Subversion repository" newuser
```

Otherwise, use the below.

```
htdigest /var/svn/svn-auth.htdigest "Subversion repository" newuser
```

Upon creating the new SVN user, remember to update the SVN ACL.

```
vi /var/svn/svn-acl.conf
```

```
[groups]
newrepository_group = existinguser, newuser
[newrepository:/]

@newrepository_group = rw
```

Next, create the actual repository.

```
cd /var/svn/repos
svnadmin create newrepository
chown apache:apache newrepository
service httpd restart
```