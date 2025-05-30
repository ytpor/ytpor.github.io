---
title: 'SSH Tunnel to RDP to Desktop in Private Network'
date: 2025-05-22T10:00:00+08:00
draft: false
url: /2025/05/ssh-tunnel-to-rdp-in-private-network
tags:
- forward
- rdp
- ssh
- tunnel
- windows
---

This setup is done on Windows 11, using WSL. We are using a private key to SSH to the server. Make sure that you have SSH to the server at least once, and has the server is listed in `~/.ssh/known_hosts`

Assuming that our server is on 218.8.8.88, and the Windows Desktop we want to access is on 10.219.80.88 (private network).

1. On `WSL`, run `ssh -N -L 13389:10.219.80.88:3389 218.8.8.88 -l username`
2. Subsequently, set the `Computer` in Remote Desktop Connection to `localhost:13389`.

```
Computer: localhost:13389
User name: Username
```
