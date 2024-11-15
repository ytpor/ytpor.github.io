---
title: 'SSH Tunnel to MySQL in Private Network'
date: 2024-10-07T10:00:00+08:00
draft: false
url: /2024/10/ssh-tunnel-to-mysql-in-private-network
tags:
- heidisql
- mysql
- ssh
- windows
---

This setup is done on Windows 11, using HeidiSQL. We are using a private key to SSH to the server. Make sure that you have SSH to the server at least once, and has the server is listed in `~/.ssh/known_hosts`

Assuming that our server is on 218.8.8.88, and our MySQL is on 10.219.80.88 (private network).

1. Open HeidiSQL.
2. Create a New Session:
    * Click on the `New` button to create a new session.
3. Configure the Session:
    * Under the Settings tab:
        * **Network Type**: Select MySQL (SSH tunnel)
        * **Hostname / IP**: 10.219.80.88
        * **User**: Enter your MySQL username.
        * **Password**: Enter your MySQL password.
        * **Port**: The default MySQL port is 3306.
    * SSH tunnel tab:
        * Check **Use SSH tunnel**
        * **SSH executable**: ssh.exe
        * **SSH Host + Port**: Enter 218.8.8.88 (the SSH server IP) and the SSH port (default is 22).
        * **Username**: Enter your SSH username.
        * **Password**: Enter your SSH password or use an SSH key if you have one.
        * **Private key file**: Locate your private key file
        * **Local port**: 3307
    * Save and Connect:
        * Click `Save` to save the session.
        * Click `Open` to establish the connection.
