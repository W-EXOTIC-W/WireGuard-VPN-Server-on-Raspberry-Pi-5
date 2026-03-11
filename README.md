VPN Setup

**Project title:** Secure Home VPN with WireGuard and Quad9 DNS provider

Name: Matheo

Date : June




Table of Contents

- [[#1. **Project Overview**|1. **Project Overview**]]
- [[#2. **Architecture Diagram**|2. **Architecture Diagram**]]
- [[#3. **Hardware & Software**|3. **Hardware & Software**]]
- [[#4. **Configuration Steps**|4. **Configuration Steps**]]
- [[#5. **Testing & Validation**|5. **Testing & Validation**]]
- [[#6. **Security Considerations**|6. **Security Considerations**]]
- [[#7. **Known Issues / Future Improvements**|7. **Known Issues / Future Improvements**]]
- [[#8. **Conclusion**|8. **Conclusion**]]



### 1. **Project Overview**

Goal: What am I building and why?

The aim of this project is to create a secure remote access VPN using WireGuard on a Raspberry Pi.

Use case: Access home network from anywhere and privately


### **Architecture Diagram**

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

### 3. **Hardware & Software**

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


### 4. **Configuration Steps**


#### A. Raspberry Pi Setup


Downloading the Raspberry Pi OS from the main website:
https://www.raspberrypi.com/software/
![[Pasted image 20250522142746.png]]


Successfully installing Raspberry Pi Imager, the utility I'll use to flash the OS onto our SD card.
![[Pasted image 20250522142818.png]]






## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 License.  
See the [LICENSE](./LICENSE) file for more information.


## 👤 Author

Matheo Aorhakajl (2025)

