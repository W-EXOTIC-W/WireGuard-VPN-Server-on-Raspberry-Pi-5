# Phase 3 – Encrypted DNS (Cloudflared DoH)

**Project title:** VPN Infrastructure Improvement – Secure DNS Upstream

**Name:** Matheo

**Date:** June

---

## Features

* Encrypted DNS queries using Cloudflared (DoH)
* Prevention of ISP DNS tracking
* Maintains existing Pi-hole filtering
* Seamless integration with existing WireGuard VPN
* Improved privacy without changing client configuration

---

## Table of Contents

- [1. Project Overview](#1-project-overview)
- [2. Architecture Diagram](#2-architecture-diagram)
- [3. Software Requirements](#3-software-requirements)
- [4. Configuration Steps](#4-configuration-steps)
- [5. Security Considerations](#5-security-considerations)
- [6. Conclusion](#6-conclusion)

---

## 1. Project Overview

The goal of this phase was to enhance the existing **Pi-hole DNS filtering setup** by encrypting upstream DNS queries using **Cloudflared (DNS over HTTPS)**.

In Phase 2, Pi-hole was introduced to provide network-wide ad blocking and DNS filtering, using Cloudflare (1.1.1.1) as the upstream resolver. While this improved filtering, DNS queries were still sent in plaintext.

This phase introduces Cloudflared to enable DNS over HTTPS (DoH), ensuring all DNS queries leaving the network are encrypted.

---

## 2. Architecture Diagram

```
Client Device
      │
      ▼
WireGuard VPN
      │
      ▼
Pi-hole
      │
      ▼
Cloudflared (DoH)
      │
      ▼
Cloudflare DNS
      │
      ▼
Internet
```

## 3. Software Requirements

Additional software introduced in this phase:

- Cloudflared – Enables DNS over HTTPS (DoH) for encrypted upstream queries

Existing components (from previous phases):

- Pi-hole (DNS filtering)
- WireGuard VPN

## 4. Configuartion Steps

I downloaded the latest Cloudflared package for the Raspberry Pi (ARM64) directly from the official GitHub releases using wget

Command Used: wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb

<img width="1462" height="1034" alt="image" src="https://github.com/user-attachments/assets/9f2378a8-8a27-478c-bd8f-d0b170fccd80" />

<img width="1422" height="990" alt="image" src="https://github.com/user-attachments/assets/64790267-34d5-46cf-9d7c-2979ad8e1931" />

I installed the downloaded Cloudflared package using dpkg, which unpacked and set up the service on the Raspberry Pi.

Command Used: sudo dpkg -i cloudflared-linux-arm64.deb

This command:

Unpacked the package

Took the .deb file and extracted its contents

Installed Cloudflared

Placed the binary (the actual program) on my Raspberry Pi system

into this directory:

/usr/local/bin/cloudflared

Registered it with the system

Added it to my system’s package database

So the OS now knows Cloudflared is installed

<img width="1518" height="671" alt="image" src="https://github.com/user-attachments/assets/cb7772c4-e300-43a0-9c73-75ddcc637170" />

<img width="658" height="477" alt="image" src="https://github.com/user-attachments/assets/f22c4ba2-424a-42e4-9cd8-73bd2d332b57" />

I created a dedicated configuration directory for Cloudflared inside the /etc directory.

Command Used: sudo mkdir -p /etc/cloudflared

This command:

Created a new directory for Cloudflared configuration

Used the -p flag to ensure the full directory path is created if it does not already exist

Placed the directory inside /etc, which is used for system configuration files

Provided a dedicated location to store Cloudflared configuration files

Ensured the setup follows standard Linux directory structure for services

<img width="655" height="472" alt="image" src="https://github.com/user-attachments/assets/e3a7de2e-3847-45e9-a35b-8d7bf23ba3c3" />

I created and opened the Cloudflared configuration file using nano.

Command Used: sudo nano /etc/cloudflared/config.yml

This command:

Created a new configuration file named config.yml for Cloudflared

Opened the file using the nano text editor

Placed the file inside the /etc/cloudflared directory

Allowed me to define the Cloudflared DNS settings

Prepared the system for configuring DNS over HTTPS (DoH)

Cloudflared DNS configuration settings to the config.yml file:

```
proxy-dns: true
proxy-dns-port: 5053
proxy-dns-upstream:
  - https://1.1.1.1/dns-query
  - https://1.0.0.1/dns-query
```

This configuration:

Enabled Cloudflared to act as a DNS proxy

Set the DNS service to listen on port 5053

Configured Cloudflare DNS servers as upstream resolvers

Ensured DNS queries are sent over HTTPS (DoH)

Provided encrypted DNS resolution for the Pi-hole setup

then saved it

```
CTRL + O
↓
Enter
↓
CTRL + X
```

<img width="1357" height="986" alt="image" src="https://github.com/user-attachments/assets/f416bd60-6ce7-4734-a989-abeecb2a875b" />

I created a systemd service file for Cloudflared to manage it as a background service.

Command Used: sudo nano /etc/systemd/system/cloudflared-dns.service

This command:

Created a new service file for Cloudflared

Opened the file using the nano text editor

Placed the file inside /etc/systemd/system, where custom system services are defined

Allowed me to define how Cloudflared should run as a background service

Prepared the system to manage Cloudflared using systemctl

I added the service configuration to the Cloudflared systemd service file:

```
[Unit]
Description=cloudflared DNS over HTTPS proxy
After=network.target

[Service]
ExecStart=/usr/local/bin/cloudflared --config /etc/cloudflared/config.yml
Restart=on-failure
User=nobody

[Install]
WantedBy=multi-user.target

```

This configuration:

Defined the service description and ensured it starts after the network is available

Specified the command to start Cloudflared using the configuration file

Configured the service to restart automatically if it fails

Set the service to run under the nobody user for security

Enabled the service to start automatically during system boot

NOTE: The ExecStart path must match the actual location of the Cloudflared binary.

I verified the correct path by running:

```
which cloudflared
```

<img width="423" height="78" alt="image" src="https://github.com/user-attachments/assets/08084219-b675-431b-838b-9240161c2361" />


I reloaded systemd, enabled the Cloudflared service, started it, and verified its status:

Commands Used:

```
sudo systemctl daemon-reload

sudo systemctl enable cloudflared-dns

sudo systemctl start cloudflared-dns

sudo systemctl status cloudflared-dns
```

These commands:

Reloaded systemd to recognise the newly created service file

Enabled the Cloudflared service to start automatically on system boot

Started the Cloudflared service for immediate use

Created the necessary symlink when enabling the service

Verified that the service is running correctly and without errors

<img width="1464" height="1005" alt="image" src="https://github.com/user-attachments/assets/2feb41dc-e7cc-4675-8706-9f042af62dfd" />

I accessed the Pi-hole web interface and reviewed the original upstream DNS server configuration.
The upstream DNS servers were originally configured to use Cloudflare:

```
1.1.1.1
1.0.0.1
```

This configuration:

Directed Pi-hole to send DNS queries directly to Cloudflare

Used standard DNS resolution over port 53 (unencrypted)

Provided fast and reliable DNS responses

Did not encrypt DNS queries between Pi-hole and the upstream resolver

I updated the Pi-hole upstream DNS configuration to use Cloudflared as a local DNS resolver.

<img width="1435" height="1015" alt="image" src="https://github.com/user-attachments/assets/5720625f-ee1e-4cfe-a206-0ffdb47598c1" />


This configuration:

Redirected Pi-hole DNS queries to the local Cloudflared service

Used 127.0.0.1 to reference the local machine (Raspberry Pi)

Specified port 5053, where Cloudflared is listening

Ensured DNS queries are forwarded to Cloudflared instead of external DNS servers

Enabled DNS over HTTPS (DoH) for encrypted upstream resolution

I reloaded the Pi-hole DNS service and monitored the logs to verify that DNS queries were being processed correctly:

<img width="1437" height="973" alt="image" src="https://github.com/user-attachments/assets/99c68d45-2c19-40ac-bd25-7afd05953cde" />


Commands Used:

```
sudo pihole restartdns reload-lists
sudo tail -f /var/log/pihole.log
```

These commands:

Reloaded the Pi-hole DNS service and applied the updated upstream configuration

Refreshed blocklists and DNS settings

Allowed Pi-hole to begin using Cloudflared for DNS resolution

Displayed real-time DNS query logs from Pi-hole

Enabled me to verify that DNS requests were being processed correctly

Confirmed that the new configuration was functioning as expected


Since Cloudflare supports DNSSEC and is used via Cloudflared, DNSSEC was effectively enabled. I verified this by testing DNS resolution while connected to the VPN:

<img width="1896" height="629" alt="image" src="https://github.com/user-attachments/assets/34593324-7408-40b4-881e-f3ff79ce3c9c" />

Testing while OFF:

<img width="1919" height="936" alt="image" src="https://github.com/user-attachments/assets/30627ab1-5657-4a5f-ba49-138d9145bf52" />


## 5. Security Considerations

DNS queries are encrypted using DNS over HTTPS (DoH) via Cloudflared

Prevents ISP and third parties from monitoring DNS traffic

Cloudflared runs under a low-privilege user (nobody) to limit system access

The service is managed through systemd, ensuring controlled startup and restart behaviour

Only local communication (127.0.0.1) is used between Pi-hole and Cloudflared, reducing exposure

Existing WireGuard VPN ensures that all client traffic remains encrypted in transit


## 6. Conclusion

This phase enhanced the existing Pi-hole and WireGuard setup by introducing encrypted DNS resolution using Cloudflared.

The improvement ensures that all DNS queries are securely transmitted while maintaining the existing filtering functionality provided by Pi-hole.

Overall, this results in a more private and secure network configuration without requiring any changes on client devices.




