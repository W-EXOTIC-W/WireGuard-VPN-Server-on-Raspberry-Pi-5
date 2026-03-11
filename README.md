VPN Setup

**Project title:** Secure Home VPN with WireGuard and Quad9 DNS provider

Name: Matheo

Date : June




## Table of Contents

- [1. Project Overview](#1-project-overview)
- [2. Architecture Diagram](#2-architecture-diagram)
- [3. Hardware & Software](#3-hardware--software)
- [4. Configuration Steps](#4-configuration-steps)
- [5. Testing & Validation](#5-testing--validation)
- [6. Security Considerations](#6-security-considerations)
- [7. Known Issues / Future Improvements](#7-known-issues--future-improvements)
- [8. Conclusion](#8-conclusion)



## 1. Project Overview

Goal: What am I building and why?

The aim of this project is to create a secure remote access VPN using WireGuard on a Raspberry Pi.

Use case: Access home network from anywhere and privately


## 2. Architecture Diagram

- **Client connects to VPN using WireGuard:**
    
    - My phone/laptop connects to my home Raspberry Pi using WireGuard.
        
    - This tunnel is encrypted end-to-end.
        
    - I configured port forwarding on my router so that even when I'm outside my house, I can still connect to my Raspberry Pi.
        
- **Client has DNS set to 9.9.9.9 (Quad9):**
    
    - I manually specified this in my WireGuard configuration.
        
    - This means **all DNS requests** (e.g. `google.com`) go to Quad9.
        
- **DNS and web traffic travel through the encrypted VPN tunnel:**
    
    - Everything from my client gets **tunneled into my Pi** at home.
        
    - This includes:
        
        - DNS requests → 9.9.9.9 (Quad9)
            
        - HTTPS/HTTP requests → websites
            
- **Raspberry Pi receives the traffic:**
    
    - It acts as my VPN gateway.
        
    - It **forwards DNS traffic to Quad9**, and other traffic to its final destination (e.g. websites).
        
- **Pi sends all outgoing traffic to my ISP (e.g. Telstra):**
    
    - My ISP sees traffic **coming from my Pi**, not from the original device.
        
    - DNS requests go to Quad9.
        
    - Other internet traffic goes to their respective sites (encrypted if HTTPS).

## 3. Hardware & Software

- Raspberry Pi model (Raspberry Pi 5 8GB RAM)
- Micro HDMI
- Mouse
- Keyboard
- SD Card (Preferably 128GB)
- Monitor
- Power Supply

------------------------

- OS : Raspberry Pi OS 64-bit

---------------

Applications Installed: WireGuard


## 4. Configuration Steps


#### A. Raspberry Pi Setup


Downloading the Raspberry Pi OS from the main website:
https://www.raspberrypi.com/software/
<img width="1919" height="992" alt="image" src="https://github.com/user-attachments/assets/f4641c40-dd38-4889-9b00-e7853d45601b" />



Successfully installing Raspberry Pi Imager, the utility I'll use to flash the OS onto our SD card.
<img width="499" height="382" alt="image" src="https://github.com/user-attachments/assets/c79cfc10-65e6-4265-99b1-914a7698b4bd" />

**Choosing Device, OS, and Storage in Imager**. Selecting the correct hardware (Raspberry Pi 5), preferred operating system (64-bit OS), and target SD card to prepare the bootable system image.

The Raspberry os 64 bit is the os I will be choosing
<img width="579" height="126" alt="image" src="https://github.com/user-attachments/assets/d1ddddd3-1322-4433-ba1e-206fbbd91c16" />

**All Set:**

<img width="680" height="482" alt="image" src="https://github.com/user-attachments/assets/eb255501-31dd-45ea-bbb1-af4bac016c24" />

**Prompt for OS Customization (Skipped)** . Choosing to skip automated OS customizations so I can configure key settings like Wi-Fi, hostname, and user credentials manually later.
<img width="679" height="482" alt="image" src="https://github.com/user-attachments/assets/ce9fe6b8-449c-4830-bb43-32dce60b8d43" />

#### B. Raspberry Pi User Setup

**Creating a New User Account** . Manually setting up a secure user account that will manage the Raspberry Pi and VPN—ensuring tight control over admin privileges.
<img width="637" height="427" alt="image" src="https://github.com/user-attachments/assets/5305de00-83cc-44c9-a60c-f65133ab22a9" />

#### C. VPN Setup

**Launching PiVPN Installer from Terminal** . Executing the PiVPN installation script, which automates setup of WireGuard on the Raspberry Pi for secure remote access.
curl -L https://install.pivpn.io | bash
<img width="1327" height="940" alt="image" src="https://github.com/user-attachments/assets/0f1db0fa-d90f-4dad-96d0-36727e513393" />

Network Interface Prompt for IP Addressing — The installer prompts me to configure the network interface and verify the static IP settings, ensuring my VPN clients can reliably reach my server.
<img width="1350" height="975" alt="image" src="https://github.com/user-attachments/assets/5bc00d94-db31-4232-a1cd-3ebaf90a818b" />

Accepting DHCP Reservation Setup — I chose to keep a DHCP reservation on my router so the Raspberry Pi always receives the same IP address without manually configuring a static IP. I set up the static lease through the router dashboard.

<img width="1373" height="956" alt="image" src="https://github.com/user-attachments/assets/ebfd09b5-ffb1-445e-89b2-458989134cc6" />

Telstra Router Dashboard Overview — After logging into my Telstra router, I accessed the main dashboard to view the network status before navigating to Advanced Settings to configure DHCP static leasing for my Raspberry Pi.

<img width="1918" height="938" alt="image" src="https://github.com/user-attachments/assets/89f4973e-06ab-4712-944e-567ea64368b2" />

Router Device List – Identifying Raspberry Pi IP Address — I opened the Devices page on my router dashboard to locate my Raspberry Pi and confirm the IP address assigned to it by DHCP.

<img width="1192" height="641" alt="image" src="https://github.com/user-attachments/assets/a2aa5b98-f842-4450-8833-3ba8aa3df559" />

Router Device List – Identifying Raspberry Pi IP and MAC Address — I located my Raspberry Pi in the router’s Devices list and recorded both its IP address and MAC address, which I need when configuring DHCP static leasing so the router always assigns the same IP to the Pi.

<img width="1919" height="901" alt="image" src="https://github.com/user-attachments/assets/255913bb-6235-40b5-8687-f8c268e2af5e" />

Local Network Settings – Configuring Static DHCP Lease — I navigated to the Local Network page on my router and added my Raspberry Pi’s MAC address and IP address to the Static Leases section so the router always assigns the same IP to the device.

<img width="1169" height="638" alt="image" src="https://github.com/user-attachments/assets/313ceda5-8628-4fc9-ba6a-958b49061352" />

The address is now reserved

<img width="1172" height="640" alt="image" src="https://github.com/user-attachments/assets/34be496c-d6f1-4f6d-85bb-0ccc0a6c3ef9" />

Selecting Raspberry Pi User for VPN Configurations — I selected the local system user that will store and manage the WireGuard VPN configuration files on the Raspberry Pi, keeping the keys and configuration centralized under that account.

<img width="1406" height="984" alt="image" src="https://github.com/user-attachments/assets/35f32270-0fd2-4771-bcee-961d97552ad9" />

Selecting VPN Protocol – WireGuard — I chose WireGuard as the VPN protocol because it provides fast connection speeds, modern cryptography, and better performance on mobile devices compared to older protocols like OpenVPN.

<img width="1343" height="942" alt="image" src="https://github.com/user-attachments/assets/e12cc1ab-0914-4f66-a6d1-469dc987948e" />

Confirming WireGuard Default Port (51820) — I kept the default WireGuard UDP port 51820, which is the standard port used by most WireGuard configurations and VPN clients.

<img width="1385" height="946" alt="image" src="https://github.com/user-attachments/assets/bc07f230-dce8-4f52-82e7-3af4b1eb6b79" />

Choosing DNS Provider (Quad9). Configuring DNS to use Quad9, a privacy-focused resolver that blocks access to malicious domains and preserves query confidentiality. This will work for VPN Clients only and not the Raspberry Pi itself so in order to have Raspberry pi use quad9 I would have to configure it manually.

<img width="1429" height="986" alt="image" src="https://github.com/user-attachments/assets/734013ef-ccdd-42d9-989f-7ca4355b7343" />

Selecting Public IP for VPN Endpoint — I chose to use my public IP address as the VPN endpoint so my WireGuard clients can connect to my home Raspberry Pi from outside the network.

<img width="1387" height="977" alt="image" src="https://github.com/user-attachments/assets/d576fc59-d367-4276-a977-791a664a4f87" />

Enabling Unattended Security Updates — I enabled unattended upgrades so my Raspberry Pi automatically installs security updates, helping keep the VPN server protected while it is exposed to the internet.

<img width="1373" height="973" alt="image" src="https://github.com/user-attachments/assets/62a63303-506c-49fa-91ee-f5860ffff224" />

**Installation Summary Screen** . Installation complete! PiVPN and WireGuard are now configured. Next step: a quick reboot to finalize setup.

<img width="1302" height="981" alt="image" src="https://github.com/user-attachments/assets/8087c736-1dc9-4c54-bed2-848abb5e1b77" />

**Reboot Prompt** . Accepting system reboot—ensuring all PiVPN changes are properly applied before creating client profiles.

<img width="1352" height="967" alt="image" src="https://github.com/user-attachments/assets/2defe4e2-1310-4d4a-aa10-2a80ec3d21c8" />


#### D. VPN Client Profile Setup

**Creating VPN Client Profile via Terminal (**`pivpn add`**)** . Creating a unique WireGuard client profile named 'Xen0tic'—generating private keys and configuration file for one device.  I have to select a client IP but I don't really mind what it is so I just press Enter. Then I had to choose a name for the profile and I chose Xen0tic and clicked enter which successfully created the configuration file which I will have to send to myself to use this VPN and if i want others to use it ill have to create new keys and manually put them on the device or run the same command again which will create new keys automatically.

<img width="1375" height="1013" alt="image" src="https://github.com/user-attachments/assets/e62ec3e1-e588-42b1-b565-ac4808a4d853" />

#### E. Wireguard Setup

Next I downloaded WireGaurd on the phone to test it:


**Generating and Scanning QR Code on Mobile** . Running `pivpn -qr` to generate a scannable QR code—allowing mobile devices to instantly import VPN settings without manual entry.

<img width="708" height="1536" alt="image" src="https://github.com/user-attachments/assets/e49efb50-ac3d-45df-bf46-97ff7b216987" />

Next I pressed the + button on the bottom right and clicked scan QR code

<img width="1080" height="500" alt="image" src="https://github.com/user-attachments/assets/3517550c-77be-4040-acdc-7ab88f16d083" />
<img width="708" height="1536" alt="image" src="https://github.com/user-attachments/assets/6c589b72-7b61-41e7-8afb-53e315237bc6" />

WireGuard Mobile App Showing Imported Config . WireGuard app successfully displays the newly imported profile, ready to establish a secure tunnel back to the Raspberry Pi.

<img width="945" height="636" alt="image" src="https://github.com/user-attachments/assets/1d41f55a-b2dc-4fbf-b21b-32023fbd5864" />

#### F. Router Configuration

Logging into Telstra Gateway Router Admin . Accessing the Telstra router’s admin interface to expose the Pi’s WireGuard service to the internet.

http://telstra.gateway/

<img width="2360" height="1711" alt="image" src="https://github.com/user-attachments/assets/495d4b63-d242-459a-8f1b-97d5a038784a" />

Navigating to Advanced Settings . Switching to the 'Advanced' tab to access WAN Services and Port Forwarding settings—where external traffic rules are defined.

<img width="1415" height="1017" alt="image" src="https://github.com/user-attachments/assets/4fa7a229-6fbc-4be1-a3cd-095ac9dea84d" />

**No Existing Port Forwards Yet** .  The router currently has no port forwarding rules set—indicating our VPN isn't externally accessible just yet.

<img width="1178" height="630" alt="image" src="https://github.com/user-attachments/assets/0eb8ec1b-25d4-444b-a075-cbef3fe5b349" />

Next I have to Click Add new IPv4 port mapping but in the below screenshot ive already added the vpn but in the future this is the section that need to be filled and to successfully add it you must click the little tiny blue plus button on the right side .

<img width="1150" height="261" alt="image" src="https://github.com/user-attachments/assets/12e0d415-5eae-4cc3-9bfe-227fcaf80c6c" />

**Configured VPN Port Forward Rule for UDP 51820** . Successfully mapping UDP port 51820 from WAN to the Raspberry Pi’s internal IP—this lets VPN clients securely reach our home server.

<img width="1171" height="639" alt="image" src="https://github.com/user-attachments/assets/ca244edd-0bd1-498d-a195-6b0f0f59c584" />

### 5. **Testing & Validation**

**Checking IP with VPN On (Mobile Device)** . Browsing to WhatIsMyIPAddress.com while VPN is active—shows external IP as the home’s IP, confirming successful tunneling.
https://whatismyipaddress.com/

<img width="708" height="1536" alt="image" src="https://github.com/user-attachments/assets/5e159ff6-6f7a-4e5b-82bd-8a23fd26c437" />

**Checking IP with VPN Off (Laptop on Home Network)** . Running the same IP test from the home network to confirm both devices appear as coming from the same source—proof the VPN works!

<img width="616" height="261" alt="image" src="https://github.com/user-attachments/assets/a4bd7f24-e728-4b89-9043-e2d8624d719d" />


## 6. Security Considerations

In this VPN configuration, **Quad9 (9.9.9.9)** was selected as the DNS resolver for its strong security posture. Quad9 blocks access to malicious domains based on threat intelligence feeds, making it a solid choice for privacy-conscious setups and baseline protection.

However, this setup did not include granular control over ad or tracker blocking. To improve on both performance and user customization, the next phase of this project involves integrating:

- **Pi-hole** as a local DNS filtering layer

- **Cloudflare (1.1.1.1)** as the upstream resolver


Cloudflare was selected for its fast resolution speeds, strong privacy policy, and support for DNS over HTTPS (DoH). When paired with Pi-hole, this combination allows for:

- Custom ad/tracker blocking

- Faster DNS resolution

- Improved local network visibility

- Optional filtering without relying entirely on third-party blocklists


This modular design allows switching upstream providers and blocklists as needed. It also keeps DNS queries internalized via the Pi-hole server while preserving speed and security through Cloudflare.

---

## 7. Known Issues / Future Improvements

- **No initial ad-blocking or DNS logging**:  DNS queries are not filtered or logged so integrating pi-hole will solve this issue.

- **Fallback DNS exposure**: Initial DNS setup used public resolvers (Quad9) directly, which may expose metadata to third-party providers.

-  **Basic firewall only**: IP whitelisting is limited; future improvements could include full `iptables` lockdown with port knock or more refined access control.

- **No user management/auditing**: VPN client usage is not currently logged or monitored, which limits accountability and auditing capability.
---

## 8. Conclusion

- **What I learned technically**
 I learned how to deploy and configure a secure VPN using WireGuard on a Raspberry Pi. This included setting up port forwarding on the router, generating server/client key pairs, configuring the WireGuard interface, and assigning a privacy-focused DNS provider (Quad9) for all connected clients. I also practiced configuring system services and troubleshooting connectivity and DNS resolution issues.

- **What this shows about my security mindset**

 This project highlights my focus on privacy and control over my network traffic. By routing all client DNS queries through a known secure provider (Quad9), I reduced reliance on my ISP's DNS and ensured encrypted resolution paths. It shows I'm security-conscious, capable of deploying secure infrastructure, and continuously think about data privacy, network segmentation, and potential future hardening strategies.

---

## Benefits of the VPN I Just Set Up on Raspberry Pi

1. **Secure Remote Access to Home Network**
    
    - I can access my home network (and Raspberry Pi) from anywhere in the world.
        
    - All data is encrypted through a secure WireGuard tunnel.
        
2. **Mobile & Laptop Traffic Is Protected**
    
    - When connected, my phone or laptop routes all internet traffic through my home IP.
        
    - Useful for using public Wi-Fi safely without risk of spying.
        
3. **Real IP Masking**
    
    - My device’s IP becomes my home internet IP (even when using mobile data).
        
    - This helps bypass restrictions that may apply to my original network.
        
4. **Works Across Any External Network**
    
    - I don’t need to configure routers outside my home — it just works wherever I am.
        
    - Useful in places where VPNs or apps are blocked.
        
5. **Can Share With Family Securely**
    
    - I’ve already generated a `.conf` file that allows others to connect securely to my VPN.
        
    - No need for third-party VPN providers or accounts.
        
6. **Port Forwarding Configured**
    
    - My router is now set to forward VPN traffic to my Pi, so it accepts remote connections at all times.
        
7. **Low Cost, High Privacy**
    
    - No subscription fees.
        
    - Data is not shared with commercial VPN services.
        
    - I own and control the entire tunnel.



## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 License.  
See the [LICENSE](./LICENSE) file for more information.


## 👤 Author

Matheo Aorhakajl (2025)

