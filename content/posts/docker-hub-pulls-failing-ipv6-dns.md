---
title: 'Docker Hub pulls failing due to IPv6 DNS'
date: 2026-05-20T10:00:00+08:00
draft: false
url: /2026/05/docker-hub-pulls-failing-ipv6-dns
tags:
- docker
- linux
- dns
- dnsmasq
---

While on Ubuntu servers, image pulls from Docker Hub kept failing with the following error:

```
Error response from daemon: Get "https://registry-1.docker.io/v2/": dial tcp
[2600:1f18:2148:bc00:c80c:3676:30dd:a616]:443: connect: cannot assign requested address
```

Running `ip addr show` confirmed the host has no IPv6 addresses on any interface. The server's network does not route IPv6.

The culprit is Docker's Go runtime. Unlike most system programs, Docker does not use the C library's `getaddrinfo()` for DNS - it has its own pure Go resolver. When DNS returns both A and AAAA records for `registry-1.docker.io`, Go prefers the IPv6 address. The kernel immediately returns `EADDRNOTAVAIL` because there is no IPv6 source address to bind, and the connection fails before any packet reaches the network.

This is why the following do not work:

- Modifying `/etc/gai.conf` to prefer IPv4 - only affects `getaddrinfo()`, which Docker bypasses
- `sysctl net.ipv6.conf.all.disable_ipv6=1` - does not prevent IPv6 socket creation
- `ip6tables -A OUTPUT -p tcp -j REJECT` - never reached, the error is a local kernel failure before packet creation

The fix is to prevent AAAA records from reaching Docker's resolver entirely, using `dnsmasq` with `filter-AAAA`.

Install dnsmasq:

```bash
sudo apt install -y dnsmasq
```

Create `/etc/dnsmasq.d/ipv4-only.conf`:

```
filter-AAAA
server=8.8.8.8
server=1.1.1.1
listen-address=127.0.0.1
bind-interfaces
```

Disable `systemd-resolved`'s stub listener so dnsmasq can take port 53, then start dnsmasq:

```bash
sudo mkdir -p /etc/systemd/resolved.conf.d

sudo tee /etc/systemd/resolved.conf.d/no-stub.conf << 'EOF'
[Resolve]
DNSStubListener=no
EOF

sudo systemctl restart systemd-resolved
sudo systemctl enable dnsmasq
sudo systemctl restart dnsmasq
```

Replace `/etc/resolv.conf` with a static file pointing at dnsmasq:

```bash
sudo rm -f /etc/resolv.conf
echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf
```

Confirm the fix by running `nslookup registry-1.docker.io` - the response should contain only IPv4 addresses. Docker pulls should now succeed consistently.
