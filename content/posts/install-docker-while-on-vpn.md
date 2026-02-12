---
title: 'Install Docker on server while connecting through VPN'
date: 2026-02-12T10:00:00+08:00
draft: false
url: /2026/02/install-docker-while-on-vpn
tags:
- docker
- vpn
---

While connected to a client's VPN, upon installing Docker on one of their server, we lost connection to that server. Initially, we thought some VPN security rules had kicked in. After further investigation, it was brought to our attention that the VPN network range conflicted with the default Docker network range.

Based on what had happened, we have updated our installation work flow to the below.

Before intalling Docker, mask the Docker service.

```bash
sudo systemctl mask docker
```

Then, install Docker.

Next, add the below content to `/etc/docker/daemon.json`

```
{
  "bip": "10.200.0.1/24",
  "default-address-pools": [
    {
      "base": "10.201.0.0/16",
      "size": 24
    }
  ]
}
```

Subsequently, unmask and restart the Docker service.

```
sudo systemctl unmask docker
sudo systemctl restart docker
```
