Raspberry Pi VPN & DNS Privacy Infrastructure

This project documents the setup and evolution of a self-hosted networking stack built on a Raspberry Pi.
It begins with a WireGuard VPN server for secure remote access and is later expanded with DNS privacy, filtering, and performance improvements.

The goal is to provide private remote access to a home network while improving DNS security, privacy, and control over network traffic.

## Skills Demonstrated
- Network configuration (VPN, DNS, port forwarding, DHCP)
- Linux system administration (Raspberry Pi)
- Troubleshooting connectivity and DNS issues
- Implementing secure remote access solutions

## Key Outcomes
- Built and configured a self-hosted VPN for secure remote access
- Implemented DNS filtering and privacy enhancements using Pi-hole and DoH
- Improved network reliability using dynamic DNS
- Diagnosed and resolved networking and DNS-related issues during setup


## Project Phases

### Phase 1 – WireGuard VPN with Quad9 DNS

The initial setup involved deploying a WireGuard VPN server on a Raspberry Pi to allow secure remote access to the home network.

Features:

WireGuard VPN server

Static DHCP lease for the Raspberry Pi

Port forwarding on the router

Quad9 DNS resolver (9.9.9.9)

Full tunnel VPN routing

Documentation:
docs/01-wireguard-vpn-setup.md

### Phase 2 – Pi-hole DNS Filtering

The second phase introduced Pi-hole as a DNS sinkhole to block advertisements and tracking domains for all VPN-connected devices.

Improvements:

Network-wide ad blocking

DNS query monitoring

Custom blocklists

VPN DNS routing through Pi-hole

Documentation:
docs/02-pihole-adblocking.md

### Phase 3 – Cloudflared DNS over HTTPS

To improve DNS privacy and encryption, the setup was further enhanced by integrating Cloudflared to route DNS queries through Cloudflare using DNS over HTTPS (DoH).

Improvements:

Encrypted DNS queries

Reduced DNS spoofing risk

Improved privacy for DNS traffic

Documentation:
docs/03-cloudflared-doh.md

### Phase 4 – Dynamic Public IP Handling

The final improvement addressed issues caused by changing public IP addresses by implementing a dynamic DNS solution.

Improvements:

Stable remote VPN access despite ISP IP changes

Automatic DNS record updates

Improved reliability of remote connections

Documentation:
docs/04-dynamic-public-ip.md
