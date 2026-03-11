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



## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 License.  
See the [LICENSE](./LICENSE) file for more information.


## 👤 Author

Matheo Aorhakajl (2025)

