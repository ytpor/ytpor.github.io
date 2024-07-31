---
title: 'Running multiple instances of MariaDB'
date: 2024-07-04T13:00:00.000+08:00
draft: false
url: /2024/07/running-multiple-instances-of-mariadb.html
tags:
- mariadb
- windows
---

We have come across an occassion where we need to run multiple `MariaDB` databases on different port on the same Windows server. For our use case, each of the database represents a particular enviroment, eg. staging, sandbox or production. The same technique that I am going to illustrate will work for `MySQL`.

Here's how it was done.

Upon installing MariaDB, we made a few copies of the data folder found in `C:\Program Files\MariaDB 10.11\data`, and named them:-

* `C:\DatabaseStaging`
* `C:\DatabaseSandbox`
* `C:\DatabaseProduction`

Next, we updated the `my.ini` within those folders. The content of `my.ini` will probably look like what's shown below.

```
[mysqld]
datadir=C:\Program Files\MariaDB 10.11\data
port=3308
default_storage_engine=innodb
innodb_buffer_pool_size=1011M
character-set-server=utf8
[client]
port=3308
plugin-dir=C:\Program Files\MariaDB 10.11\lib\plugin
```

Assuming that we will run each of the instance on a different port, eg. 33008, 33009, 33010, we will need to update the `port` number and the value of `datadir`.

So, for `C:\DatabaseStaging\my.ini`, it would look something like this. You'll have to update the other `my.ini` accordingly.

```
[mysqld]
datadir=C:\DatabaseStaging
port=33008
default_storage_engine=innodb
innodb_buffer_pool_size=1011M
character-set-server=utf8
[client]
port=33008
plugin-dir=C:\Program Files\MariaDB 10.11\lib\plugin
```

Now that the setup is done, we'll need to add the services.

Here's the PowerShell command to add the `DatabaseStaging` service. Change the command accodingly for the other services. Take note of the backticks that is being used to handle spaces within the path.

```shell
New-Service -Name "DatabaseStaging" -BinaryPathName "`"C:\Program Files\MariaDB 10.11\bin\mysqld.exe`" --defaults-file=C:\DatabaseStaging\my.ini DatabaseStaging" -DisplayName "DatabaseStaging" -Description " Staging MariaDB Service" -StartupType Automatic
```

After adding the services, start those services, and we are done.
