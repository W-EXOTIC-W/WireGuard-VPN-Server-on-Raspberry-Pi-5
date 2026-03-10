# WireGuard VPN Setup on Raspberry Pi

<img width="1104" height="801" alt="image" src="https://github.com/user-attachments/assets/c441d6d1-ee07-474c-beb5-67130f1896d5" />


This project documents the setup and configuration of a WireGuard VPN server hosted on a Raspberry Pi, with Quad9 DNS for secure, encrypted DNS queries. The aim is to provide private, remote access to your home network while ensuring DNS privacy and tunneling all outbound traffic through your home IP.

## 🔒 Features

- ✅ Secure remote access using WireGuard
- 🌐 Full-tunnel traffic routing for privacy
- 🧅 DNS privacy using [Quad9](https://www.quad9.net/)
- 🍓 Raspberry Pi as a lightweight self-hosted VPN server
- 🔧 Port forwarding configuration and IP verification steps
- 📌 Security considerations and upgrade paths (e.g. Pi-hole)

## 📄 Files

- `VPN Setup.pdf` – Complete documentation with screenshots, command outputs, and setup logic
- `README.md` – This project overview

## 🔐 Security Notes

- DNS requests are routed securely via Quad9 to prevent ISP snooping
- The Raspberry Pi acts only as a forwarder (no DNS logs)
- VPN masks location and traffic contents from local network or public Wi-Fi snoopers

## 🚧 Future Improvements

- Add Pi-hole for ad blocking and DNS filtering
- Switch to Cloudflare DoH/DoT with DNSCrypt
- Auto-reconnect script for persistent VPN usage

## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 License.  
See the [LICENSE](./LICENSE) file for more information.


## 👤 Author

Matheo Aorhakajl (2025)

