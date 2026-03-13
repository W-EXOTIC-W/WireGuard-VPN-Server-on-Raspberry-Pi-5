# Phase 2 – Pi-hole DNS Filtering with Cloudflare

Project title: VPN Infrastructure Improvement – DNS Filtering

Name: Matheo  
Date: June

## Features

- Network-wide ad blocking using Pi-hole
- DNS query logging and monitoring
- Custom blocklists
- Integration with existing WireGuard VPN
- Cloudflare used as upstream DNS resolver

## Table of Contents

-  [1. Project Overview](#1-project-overview)
-  [2. Architecture Diagram](#2-architecture-diagram)
-  [3. Software & Hardware Requirements](#3-software-&-hardware-requirements)
-  [4. Configuration Steps](#4-configuration-steps)
-  [5. Security Considerations](#5-security-considerations)
-  [6. Known Issues / Troubleshooting](#6-known-issues--troubleshooting)
-  [7. Future Improvements](#7-future-improvements)
-  [8. Conclusion](#8-conclusion)


## 1. Project Overview 

The goal of this phase was to improve the original WireGuard VPN setup by introducing DNS filtering and advertisement blocking.

The original VPN configuration used Quad9 as the DNS resolver. While Quad9 provides privacy-focused DNS resolution, it does not block advertisements or tracking domains.

To address this limitation, Pi-hole was deployed on the Raspberry Pi to act as a DNS sinkhole. This allows DNS queries from VPN clients to be filtered before being resolved by an upstream DNS provider.

In this configuration, Pi-hole handles DNS filtering while Cloudflare is used as the upstream resolver to provide fast and reliable DNS resolution.

## 2. Architecture Diagram

The introduction of Pi-hole changes how DNS traffic is handled. Instead of sending DNS queries directly to Quad9, all DNS requests are now routed through Pi-hole first, allowing ad and tracker domains to be filtered before reaching the upstream DNS resolver.

```
Client Device
      │
      │ Encrypted WireGuard Tunnel
      ▼
Raspberry Pi (VPN Gateway)
      │
      ▼
Pi-hole (DNS filtering & logging)
      │
      ▼
Cloudflare DNS (1.1.1.1)
      │
      ▼
Internet
```

## 3. Software & Hardware Requirements

### Hardware

The hardware used in this phase remains the same as the original WireGuard VPN setup:

- Raspberry Pi 5 (8GB RAM)
- MicroSD Card (128GB recommended)
- Power Supply
- Router with DHCP configuration access

### Software

Additional software introduced in this phase:

- **Pi-hole** – DNS sinkhole used for ad and tracker blocking
- **Cloudflare DNS (1.1.1.1)** – Configured as the upstream DNS resolver

Existing software from Phase 1:

- **Raspberry Pi OS (64-bit)**
- **WireGuard VPN**


## 4. Configuration Steps

First, I install **Pi-hole**, which I’m going to use as my local DNS sinkhole for ad blocking and filtering. I run the official installation script:

curl -sSL https://install.pi-hole.net | bash

After running the install command, Pi-hole shows a prompt telling me it needs a static IP. Since I already have one set from the WireGuard setup, I just select Continue

<img width="1142" height="1065" alt="image" src="https://github.com/user-attachments/assets/a9492e18-0a66-4904-a500-c8e81a14cf64" />

Next, Pi-hole asks which network interface it should use. Since my Raspberry Pi is connected to the network via Wi-Fi, I selected **wlan0** as the interface.

**Note:** In the screenshot below, **eth0 appears selected**, but during the actual installation I selected **wlan0** because the Raspberry Pi was connected via Wi-Fi.

<img width="1135" height="1056" alt="image" src="https://github.com/user-attachments/assets/c54f14aa-4034-40cb-8a58-adcacc7a132e" />

Next, Pi-hole asks me to pick an upstream DNS provider (screenshot below). It shows a list of the usual options, but since I’m improving my original WireGuard setup — which used Quad9 — I switch over to Cloudflare. Cloudflare gives me lower latency on my network and pairs well with the ad-blocking setup I’m building here, so that’s the one I select.

**Note:** In the screenshot below, the cursor is positioned on **Google**, but during the installation I selected **Cloudflare (DNSSEC)** as the upstream DNS resolver.

<img width="1124" height="1166" alt="image" src="https://github.com/user-attachments/assets/239b4256-ddc5-423d-9373-d2dfa4518b3c" />

Pi-hole then asks me if I want to include the default ad-blocking list (screenshot below). It’s StevenBlack’s Unified Hosts List, which is a solid starting point for filtering ads and trackers. Since this setup is meant to improve the original project with proper ad blocking, I select **Yes** to include it.

<img width="1136" height="1184" alt="image" src="https://github.com/user-attachments/assets/fc5dc4fa-86b0-4f12-9a93-68e0a8c0a61c" />

Next, Pi-hole asks me whether I want to enable query logging (screenshot below). Since I actually want to monitor what domains are being blocked and see how Cloudflare + the blocklists are performing, I choose Yes.

<img width="1116" height="1191" alt="image" src="https://github.com/user-attachments/assets/3523840e-78ed-473e-a52f-3da74a91d64d" />

Pi-hole now asks me to choose a privacy mode for how much detail it logs (screenshot below). Since I'm still in the process of improving this setup and want to see exactly what domains are being queried, blocked, or forwarded through Cloudflare, I stick with **Show everything**.  
This gives me full visibility into both the domains and the clients making the requests, which makes troubleshooting and fine-tuning way easier.

<img width="1101" height="1029" alt="image" src="https://github.com/user-attachments/assets/4f279729-da3e-4d77-ae08-e93593a7cdce" />

After confirming the privacy settings, Pi-hole starts installing all the core components (screenshot below). It updates the package lists, installs dependencies, pulls the latest Pi-hole and web interface files from GitHub, and sets up the required `pihole` user and group.  
Everything here is automatic so I just let the installer run until it finishes.

<img width="1148" height="1362" alt="image" src="https://github.com/user-attachments/assets/63303a43-e88b-40f9-9287-cecf6f1db0cb" />

<img width="1117" height="1162" alt="image" src="https://github.com/user-attachments/assets/7a08b4c0-20f9-4f16-913c-cd4db02cad45" />

The installer then shows the completion screen (screenshot below). It displays the IPv4/IPv6 addresses Pi-hole will use and provides the URL for the web dashboard, along with the generated admin password.  
With Pi-hole fully installed, my next step is to update my WireGuard client configurations so they route their DNS requests through Pi-hole (and ultimately Cloudflare). The generated password was changed to something more secure.

<img width="1146" height="1009" alt="image" src="https://github.com/user-attachments/assets/832e2dbc-1755-4885-89b6-f80b1c8deeea" />

This is what it is currently on which includes the main DNS resolver 9.9.9.9 and 149.112.112.112 which is the secondary resolver by Quad9:

<img width="708" height="1536" alt="image" src="https://github.com/user-attachments/assets/4f7ab377-8881-422e-a856-d5608053dc75" />

Now I've changed it to 192,168.0.135. This DNS currently only functions because the AllowedIps under peer is 0.0.0.0/0 so all traffic from my devices will go through the tunnel and be able to reach the address 192.168.0.135. 

<img width="708" height="1536" alt="image" src="https://github.com/user-attachments/assets/3eae0bd1-e72e-4e96-b30b-aa4326bb3a0e" />

Next I downloaded Wireguard on the laptop and edited the configuration to use the new DNS server so I could test the Ad Blocking. I used the same keys as I had on my phone just for testing.

<img width="1126" height="823" alt="image" src="https://github.com/user-attachments/assets/8bd7adb9-b64b-44af-87d0-3f7c1ccfe88f" />

I opened the Browser and opened the Whatsmyipaddress site and checked if ads still appear and as you seee theyre still there and after some troublshooting it turns out that blocklists were not added at all from the beginning so my next step was to edit the blocklists and add my own    blocklists from the pi-hole dashboard.

<img width="1392" height="627" alt="image" src="https://github.com/user-attachments/assets/bebf9676-320f-4a1d-bfbd-b8b630d981c0" />

I opened the pi-hole dashboard on my raspberry pi at 192.168.0.135/admin and logged in and then navigated to the lists Tab and as you see there is no subscribed lists. Also as I clicked on groups tab I would receive an error which hints that the pi hole garvity database that holds the blocklists and groups was either corrupted or incomplete. So I just decided to add my own blocklists one by one and clicked Add blocklist:

https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
https://dbl.oisd.nl/
https://adaway.org/hosts.txt
https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/SmartTV.txt
https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/microsoft.txt
https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt

<img width="1533" height="1037" alt="image" src="https://github.com/user-attachments/assets/326adfeb-4100-486d-883f-3c93c8e34481" />

<img width="1156" height="1281" alt="image" src="https://github.com/user-attachments/assets/d55a4efc-8357-4854-80c6-2591be9a77f3" />

After adding all of them I just went back to the terminal on my pi and typed pihole -g which will rebuild the database and download the blocklists I've added. This fixed the error and also downloaded the blocklists. However this means that the blocklist was probably there and all I needed to do was to rebuild but anyways I needed to add more blocklists just in case.

<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/a02f3d06-76fc-40d1-ad73-b9757309060c" />

<img width="685" height="691" alt="image" src="https://github.com/user-attachments/assets/aa672385-1830-4626-9a4e-8823f37b949f" />

Now there is no more ads:

<img width="1919" height="945" alt="image" src="https://github.com/user-attachments/assets/ddcaa9ce-3d4a-4f63-8388-f3b462632ddb" />

Pi-hole query log showing DNS requests originating from a WireGuard VPN client (10.102.115.3). The log confirms that DNS queries from the VPN tunnel are being processed by Pi-hole and filtered according to the configured blocklists.

<img width="1250" height="934" alt="image" src="https://github.com/user-attachments/assets/88e3ae81-9e6f-4841-9e18-c078691cc2da" />

During initial testing, the same WireGuard client configuration used on the phone was temporarily imported on the laptop. Later, a dedicated peer was created for the laptop, which resulted in a different VPN client IP appearing in the Pi-hole query log.


## 5. Security Considerations

Introducing Pi-hole adds an additional layer of control over DNS traffic in the network. Instead of allowing VPN clients to query external DNS servers directly, all DNS requests are routed through the local Pi-hole instance.

This provides several security and privacy benefits:

- **DNS filtering:** Known advertising and tracking domains are blocked using curated blocklists.
- **Centralized DNS control:** All VPN clients use a single DNS resolver managed within the home network.
- **Improved visibility:** The Pi-hole dashboard allows monitoring of DNS queries to identify potentially suspicious or unwanted domains.
- **Reduced reliance on public DNS providers:** Queries are filtered locally before being forwarded to the upstream resolver.

By routing VPN client DNS requests through Pi-hole, the setup improves both privacy and control over network traffic.

## 6. Known Issues / Troubleshooting

During testing, accessing the Pi-hole dashboard via the Raspberry Pi's LAN address (`192.168.0.135`) while connected to both the local Wi-Fi network and the WireGuard VPN on Windows resulted in a routing conflict.

This occurred because the WireGuard configuration routes all traffic through the VPN tunnel, causing Windows to prefer the VPN interface for network traffic. As a result, direct LAN access to the Pi-hole dashboard was blocked.

The issue was resolved by accessing the dashboard through the VPN interface address:

http://10.102.115.1/admin

This ensures that traffic reaches the Raspberry Pi through the WireGuard tunnel rather than the local network interface.

## 7. Future Improvements

While the current setup provides secure remote access and DNS filtering through Pi-hole, several improvements can further enhance the system.

- **Additional WireGuard Peers**  
  Create dedicated peers for each device instead of reusing configurations during testing. This improves client identification and access management.

- **Cloudflared (DNS over HTTPS)**  
  Integrate Cloudflared to encrypt DNS queries between Pi-hole and Cloudflare using DNS over HTTPS (DoH), improving privacy and preventing DNS interception.

- **Dynamic Public IP Handling (DuckDNS)**  
  Implement a dynamic DNS solution such as DuckDNS to handle changes in the home network's public IP address. This ensures the VPN endpoint remains reachable even if the ISP assigns a new IP.

These improvements will further strengthen the reliability, privacy, and scalability of the home VPN and DNS infrastructure.

## 8. Conclusion

This phase of the project enhanced the original WireGuard VPN setup by introducing Pi-hole as a DNS filtering layer. By routing VPN client DNS traffic through Pi-hole and forwarding allowed queries to Cloudflare, the system now provides network-wide ad blocking and improved visibility into DNS activity.

Testing confirmed that VPN clients successfully resolve domains through Pi-hole and that advertising and tracking domains are blocked according to the configured blocklists.

This improvement strengthens both privacy and control over DNS traffic while maintaining the secure remote access provided by the WireGuard VPN.


