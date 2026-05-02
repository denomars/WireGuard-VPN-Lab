# WireGuard-VPN-Lab
A fully functional Wireguard VPN server built from scratch on Ubuntu Server,  deployed in a Virtualbox VM and connected to a Windows client over an encrypted tunnel.

Project Overview:
This project demonstrates how to build and configure a Wireguard VPN server on Ubuntu Server running inside VirtualBox. The goal was to gain hands-on experience with Linux networking, VPN Protocols, encryption key management, IP forwarding, and secure remote access via SSH. All core skills in network administration and cybersecurity.

Technologies Used:
Tool:                              Purpose:
Ubuntu Server 26.04 LTS            Linux Server OS
WireGuard                          VPN Protocol
VirtualBox                         Virtualization Platform
OpenSSH                            Secure remote access to the VM
iptables                           Firewall and NAT configuration
systemctl                          Service Management
netplan                            Network Interface configuration

Network Architecture:
[Windows Machine]
Wireguard Client (10.0.0.2)
         |
Encrypted Tunnnel
(ChaCha20 / Wireguard Protocol)
         |
[VirtualBox NAT]
Port Forwarding: 51820 UDP, 2222 TCP
         |
[Ubuntu Server VM]
Wireguard Server (10.0.0.1)
NAT Address: 10.0.2.15
         |
Internet

Key Concepts I learned during this project:
- VPN Tunneling: Encrypted point-to-point communication between client and server
- Public/Private Key Cryptography: WireGuard uses ChaCha20 encryption with key pairs for authentication
- IP Forwarding: Enabling the Linux kernel to route packets between interfaces
- NAT & Port Forwarding: Forwarding traffic from the host machine into VM
- Split Tunneling: Routing only VPN subnet traffic through the tunnel while keeping regular internet traffic on the normal connection
- Kill Switch: Blocking all untunneled traffic to prevent data leaks if the VPN drops
- iptables: Configuring firewall rules and masquerading for traffic forwarding
- SSH Remote Access: Managing the Linux server remotely from Windows Terminal
- Linux Pipe Operator: Chaining commands to generate and save encryption keys
- systemctl: Starting, enabling, and verifying services on Linux

Project Phases
Phase 1 - Linux VM Setup
- Installed VirtualBox on Windows (Use the latest version)
- Deployed Ubuntu Server 26.04 LTS as a VM (Search on your favorite search engine "Ubuntu Server")
- Configured NAT networking with port forwarding for SSH (port 2222) and WireGuard (Port 51820 UDP)
- Enabled OpenSSH for remote terminal access from Windows

Phase 2 - WireGuard Server Configuration
- Installed WireGuard on Ubuntu Server (Use the command "sudo apt install wireguard -y")
- Generated server public/private key pair using Linux pipes and the tee command ("wg genkey | sudo tee /etc/wireguard/privatekey | wg pubkey | sudo tee /etc/wireguard/publickey")
- Make sure to allowed permission to used Wireguard PrivateKey ("sudo chmod 600 /etc/wireguard/privatekey")
- Created the WireGuard interface configuration (wg0.conf) with VPN subnet 10.0.0.1/24
- Configured PostUp and PostDown iptables rules for traffic forwarding
- Enabled IPv4 forwarding via /etc/sysctl.conf
- Started and enabled WireGuard as a system service

Phase 3 - Windows Client Setup
- Installed WireGuard for Windows
- Generated client key pair
- Configured the client tunnel with:
  ~ VPN IP: 10.0.0.2/24
  ~ DNS: 1.1.1.1 (Cloudflare, for privacy reasons)
  ~ Spilt tunneling via AllowedIPs = 10.0.0.0/24, Sidenote: I encountered an issue when using 0.0.0.0/0 for AllowedIPs and it wasn't connecting to internet, it creates a loop that kills your connection when running NAT inside VirtualBox, it is called routing conflict.
  ~ Kill switch enabled
  ~ Added client as a peer on the server
  ~ Then you can also ssh on your windows terminal "ssh -p 2222 yourusername@localhost ip"

Phase 4 - Verification
- Confirmed WireGuard service running ("sudo systemctl status wg-quick@wg0)
- Verified encrypted traffic flowing via ("sudo wg show") (transfer:received/sent)
- Succesfully pinged client from server (10.0.0.2) with 0% packet loss
- Confirmed tunnel active on Windows WireGuard Client

Security Notes
- Private keys are excluded from this repository, don't ever show your private keys in public no matter what
- The wg0.conf file is also not inlcuded in this repository
- Permissions on the private key were set to 600 (owner read/write only)
- Kil Switch prevents traffic leaks if the VPN connection drops (Highly important when your connection goes south!)

What I Learned
- How WireGuard works at the protocol level compared to OpenVPN and IPSec
- How Linux handles network interfaces, routing, and IP Forwarding
- The difference between NAT and Bridged networking in virtualized environments
- How to manage Linux services with systemctl
- How iptables rules control traffic flow and masquerading
- The importance of least-privilege principles when handling cryptographic keys

Next Steps 
- Going to deployed WireGuard on a cloud VPS for a public-facing VPN server
- Add multiple peer clients
- Set up network monitoring with Zabbix or Nagios
- Document with WireShark Packet captures showing encrypted vs unencrypted traffic

Screenshots of my project :)
<img width="1918" height="387" alt="sudo wg show" src="https://github.com/user-attachments/assets/101bb080-5de3-4fd8-bf07-c1e3f1f474e4" />
<img width="822" height="647" alt="Windows WireGuard Client Active" src="https://github.com/user-attachments/assets/a9e128a9-33be-44b0-a06d-428c496704ea" />
<img width="1447" height="887" alt="FastFetch" src="https://github.com/user-attachments/assets/a0027e2a-dfc0-46f9-ba63-39c8b1ccfdd9" />
<img width="731" height="658" alt="Pinging" src="https://github.com/user-attachments/assets/1f9b1211-f862-42ea-ac11-8787b417e2f9" />

