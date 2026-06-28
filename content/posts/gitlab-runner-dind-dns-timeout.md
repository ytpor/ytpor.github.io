---
title: 'GitLab Runner Docker-in-Docker image pulls failing with DNS timeout'
date: 2026-06-23T10:00:00+08:00
draft: false
url: /2026/06/gitlab-runner-dind-dns-timeout
tags:
- docker
- gitlab
- dns
- ci
---

While running CI jobs through a GitLab Runner, image pulls kept failing with the following error:

```
WARNING: Failed to pull image with policy "always": Error response from daemon: Get "https://registry-1.docker.io/v2/": dial tcp: lookup registry-1.docker.io on 127.0.0.1:53: read udp 127.0.0.1:51656->127.0.0.1:53: i/o timeout
ERROR: Job failed: failed to pull image "docker:28.5.2-dind" with specified policies [always]
```

The host machine's `/etc/resolv.conf` had `nameserver 127.0.0.1` - a local resolver that only works on the host itself.

When the GitLab Runner spawned a Docker-in-Docker (DinD) service container, the inner Docker daemon inside DinD had no `daemon.json` of its own. It read `/etc/resolv.conf` and inherited `127.0.0.1:53` as its nameserver. Inside a container, `127.0.0.1` is the container's own loopback interface - nothing listens on port 53 there - so every DNS lookup timed out and image pulls failed.

The host's `/etc/docker/daemon.json` did have `1.1.1.1` and `8.8.8.8` configured, but that only governs DNS for containers spawned by the host Docker daemon. The inner DinD daemon is a separate process that does not inherit that setting. Checking `/etc/resolv.conf` inside the runner container itself showed Docker's embedded DNS at `127.0.0.11`, which was healthy - the problem was one layer deeper, inside DinD.

The fix is to pass explicit DNS servers through the GitLab Runner config so every spawned container, including DinD, gets real nameservers. Add the following to each `[runners.docker]` section in `/etc/gitlab-runner/config.toml`:

```toml
[runners.docker]
  dns = ["8.8.8.8", "1.1.1.1"]
```

To apply this without a shell inside the runner container:

```bash
docker cp <runner-container-id>:/etc/gitlab-runner/config.toml /tmp/config.toml
vi /tmp/config.toml
docker cp /tmp/config.toml <runner-container-id>:/etc/gitlab-runner/config.toml
docker exec <runner-container-id> gitlab-runner restart
```

Image pulls should succeed after the runner restarts.
