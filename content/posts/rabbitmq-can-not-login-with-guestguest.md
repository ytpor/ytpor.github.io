---
title: 'RabbitMQ can not login with guest/guest'
date: 2016-12-20T13:54:00+08:00
draft: false
url: /2016/12/rabbitmq-can-not-login-with-guestguest.html
tags:
- ubuntu
- rabbitmq
cover:
  image: "https://upload.wikimedia.org/wikipedia/commons/7/71/RabbitMQ_logo.svg"
  alt: "RabbitMQ"
  relative: false
---

After a fresh install of RabbitMQ, I have encountered a Login Failed error when trying to access the RabbitMQ Management console at http://localhost:15672

Here's one possible solution to overcome this issue.

Create a file **/etc/rabbitmq/rabbitmq.config**, and add the below content into that file.

```
[{rabbit, [{loopback_users, []}]}].
```

Restart the service to apply the changes.