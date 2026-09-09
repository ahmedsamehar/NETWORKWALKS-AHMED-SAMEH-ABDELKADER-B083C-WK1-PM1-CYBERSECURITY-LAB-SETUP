# Networkwalks - WK1-PM1: Cybersecurity Lab Setup
 
## 👤 Student Info
- **Name:** Ahmed Sameh Abdelkader
- **Batch:** B083C
- **Task:** WK1-PM1 - Lab Setup (VirtualBox + Kali Linux)
## 🎯 Task Objective
Set up a Cybersecurity testing lab environment with the following specs:
- VirtualBox (latest version) as the base hypervisor
- Kali Linux as the attacking/hacker machine
- Custom NAT Network in subnet `10.0.0.0/24`
- Kali Linux IP set manually to `10.0.0.2/24`
- Shared Clipboard & Drag/Drop enabled
- Shared folder (`/downloads`) mounted from host
- Full internet access verified on Kali Linux
## 🛠️ Steps I Followed
 
### 1. Installed Prerequisites
- Installed 7-Zip
- Installed the latest version of VirtualBox
### 2. Created the NAT Network
Created a custom NAT Network named `NatNetwork` with:
- IPv4 Prefix: `10.0.0.0/24`
- DHCP: Enabled
![NAT Network Settings](screenshots/nat-network-settings.png)
 
### 3. Imported Kali Linux VM
Downloaded and imported the official Kali Linux VirtualBox image.
 
![VM Details](screenshots/vm-details.png)
 
### 4. Attached Kali to the NAT Network
Changed the VM's Network Adapter from default NAT to **NAT Network** → `NatNetwork`.
 
### 5. Configured Static IP on Kali Linux
Set the IPv4 configuration manually inside Kali:
- Address: `10.0.0.2`
- Netmask: `24`
- Gateway: `10.0.0.1`
- DNS: `8.8.8.8`
![IPv4 Manual Settings](screenshots/ipv4-settings.png)
 
### 6. Verified Connectivity
Confirmed the IP was applied and tested connectivity to the gateway and the internet.
 
```bash
ip a
ping -c 4 10.0.0.1
ping -c 4 8.8.8.8
```
 
![Ping Results](screenshots/ping-results.png)
 
### 7. Took a Snapshot
Took a VM snapshot after confirming the network was fully working, to preserve this clean configured state.
 
## 🐞 Troubleshooting Experience
 
**Problem:**
After configuring the static IP (`10.0.0.2/24`) on the Kali Linux VM, running `ip a` showed the interface `eth0` as `UP`, but no `inet 10.0.0.2/24` line appeared - only an `inet6 ... tentative` address was listed. This meant the IPv4 address wasn't actually being applied, even though the settings looked correct. This is a known issue in VirtualBox v7 with Kali Linux 2026.1+, caused by IPv4 Duplicate Address Detection (DAD) timing out.
 
**Solution:**
Ran the following commands to disable the DAD timeout and reset the connection:
 
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```
 
After this, `ip a` correctly showed `inet 10.0.0.2/24` on `eth0`, and both `ping 10.0.0.1` (gateway) and `ping 8.8.8.8` (internet) succeeded with 0% packet loss.
 
## ✅ Result
Kali Linux VM is fully connected to the custom NAT Network with a static IP, full internet access, shared clipboard/drag-drop, and a shared folder from the host - ready for future lab tasks.
 
