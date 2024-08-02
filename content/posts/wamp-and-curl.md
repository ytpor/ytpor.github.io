---
title: 'WAMP and curl'
date: 2018-02-09T17:33:00.001+08:00
draft: false
url: /2018/02/wamp-and-curl.html
tags:
- curl
- wamp
cover:
  image: "https://upload.wikimedia.org/wikipedia/commons/4/4f/WampServer.png"
  alt: "WAMP"
  relative: false
---

Often times, after a fresh install of WAMP server, curl stops working. You might get a hint of the issue in logs/php\_error.log If not, it could be that the SSL/TLS Certificate Authority (CA) certificates has not bee setup properly.

1.  Download [cacert.pem](https://curl.haxx.se/ca/cacert.pem)
2.  Move the cacert.pem file to a folder, e.g. C:\\code\\ssl\\cacert.pem
3.  Edit the php.ini file and change the curl.cainfo parameter, e.g. curl.cainfo = "C:\\code\\ssl\\cacert.pem"