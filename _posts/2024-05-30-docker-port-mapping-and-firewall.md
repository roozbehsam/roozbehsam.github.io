---
title: Does Docker Port Mapping Bypass the Host Firewall?	
date: 2024-05-30 00:00 +0330
description: Docker port mapping and firewall.
category: [Notes]
tags: [docker, devops, fiewall, linux]
published: true
sitemap: true
---

When using Docker, many encounter the unexpected behavior of port mapping bypassing the host's firewall rules. This phenomenon can be perplexing, especially for those relying on firewall tools like UFW (Uncomplicated Firewall).

#### The Problem
Consider this scenario:
- You have a Docker container running an application (e.g., urbackup) with the following port mapping:

  ```yaml
  ports:
    - 5000:5000
  ```
- The application becomes accessible at `192.168.0.30:5000` without any explicit rules in UFW allowing this traffic.

Alternatively, if you use:

  ```yaml
  network_mode: "host"
  ```
the application is inaccessible until UFW rules are explicitly added.

#### Why This Happens
Docker manages its own `iptables` rules to handle network isolation and routing for containers. It places these rules in specific firewall chains that take precedence over user-defined chains like those used by UFW. Docker's rules allow traffic to the mapped ports directly, bypassing the usual host firewall rules.

#### Explanation
- **Docker's `iptables` Chains**: Docker manipulates the `iptables` rules by inserting its own rules in a chain that has a higher precedence than typical user-defined chains.
- **Chain Precedence**: Firewall chains have a hierarchy. Docker places its rules in the `DOCKER` chain, which sits above the user-defined chains altered by UFW.
- **Host Mode**: When using `network_mode: "host"`, the container uses the host’s network stack directly, meaning UFW rules apply as expected.

#### Solutions
1. **Custom `iptables` Rules**: Place custom firewall rules in the `DOCKER-USER` chain to ensure they are processed before Docker’s rules.
2. **Local Address Binding**: Bind Docker ports to local addresses and use a reverse proxy like NGINX to manage access control.
3. **Modify Docker Configuration**: Adjust Docker’s default behavior by configuring Docker to not manipulate `iptables`.

#### Example Solution
To prevent Docker from bypassing UFW, you can create a custom `iptables` rule. For instance:
```sh
iptables -I DOCKER-USER -p tcp --dport 5000 -j DROP
```
This rule blocks all traffic to port 5000 unless explicitly allowed elsewhere.

#### Conclusion
Understanding the interaction between Docker’s port mapping and host firewall rules is crucial for securing Dockerized applications. By leveraging custom `iptables` rules and configuring Docker appropriately, you can maintain the desired level of security without unexpected traffic bypassing your firewall settings.

For more detailed guidance, refer to the [Docker documentation on packet filtering and firewalls](https://docs.docker.com/network/packet-filtering-firewalls/).
