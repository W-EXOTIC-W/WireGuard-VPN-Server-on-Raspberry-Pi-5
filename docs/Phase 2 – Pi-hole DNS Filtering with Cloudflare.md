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
-  [3. Software Requirements](#3-software-requirements)
4. Configuration Steps
5. Testing & Validation
6. Security Considerations
7. Known Issues / Troubleshooting
8. Conclusion


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

## 3. Software Requirements

The following software and components were required to implement Pi-hole DNS filtering on the existing WireGuard VPN infrastructure.

### Operating System
- Raspberry Pi OS (64-bit)

### Applications Installed
- **WireGuard** – Already installed from Phase 1 to provide secure VPN tunneling.
- **Pi-hole** – Used as a DNS sinkhole to filter advertisements and tracking domains.
- **Cloudflare DNS (1.1.1.1)** – Configured as the upstream DNS resolver for Pi-hole.

### Hardware
- Raspberry Pi 5 (8GB RAM)
- Stable internet connection
- Router with port forwarding already configured (from Phase 1)

### Network Requirements
- Static DHCP lease configured on the router for the Raspberry Pi
- Existing WireGuard VPN configuration
- Access to the Pi-hole web interface via the Raspberry Pi's local IP address


