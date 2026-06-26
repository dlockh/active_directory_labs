# Lab 1: Virtual Machine Setup

## Overview
<img width="962" height="748" alt="VirtualBox Screen" src="https://github.com/user-attachments/assets/7d85b47a-4bd6-4130-a809-24f3c5c0cb5f" />

The goal of this lab was to fully setup the Active Directory home lab environment inside of VirtualBox before I got to configuring anything. 
I downloaded all of the ISOs and did the initial setups of all 4 virtual machines (VMs):
- **Target Client Machine:** Windows 10 (from Microsoft's official media creation tool)
- **Domain Controller:** Windows Server 2022 (from Microsoft Evaluation Center)
- **Attacker Machine:** Kali Linux (from kali.org - their prebuilt VirtualBox image)
- **Log Monitoring:** Ubuntu Server (from ubuntu.com - for the Splunk VM)

## Specs

### Windows 10 - Target Client Machine:
- **Type:** Microsoft Windows/Windows 10 (64-bit)
- **RAM:** 4096 MB
- **CPUs:** 1 
- **Disk:** 50 GB
- **Network:** Internal Network to route through the Domain Controller later... 

### Windows Server 2022 - Domain Controller:
- **Type:** Microsoft Windows/Windows 2022 (64-bit)
- **RAM:** 2048 MB
- **CPUs:** 1 
- **Disk:** 50 GB
- **Network:** NAT (keeps the VM isolated from external exposure while still allowing internet access) 

### Kali Linux - Attacker Machine:
- **Type:** Linux/Debian (64-bit)
- **RAM:** 2048 MB
- **CPUs:** 2 
- **Disk:** 50 GB
- **Network:** NAT

### Ubuntu - Splunk:
- **Type:** Linux/Ubuntu (64-bit)
- **RAM:** 8192 MB
- **CPUs:** 2 
- **Disk:** 100 GB
- **Network:** NAT

## Steps Taken for Each VM
1. Opened up VirtualBox and clicked **New**.
2. Named the VM and selected the correct OS type & version.
3. Attached the corresponding ISO.
4. Set the RAM, CPU, and disk size specs.
5. Booted the VM and completed the OS installation.
6. Took a snapshot after each clean install and labeled as "Fresh Install" to have a baseline to refer/revert to if necessary.

#### Security Tip with Downloading VirtualBox
When downloading VirtualBox, use the SHA256 checksums to verify that the download has not been altered:
1. Download the SHA256 checksums file from VirtualBox.
2. Go to your local Downloads directory.
3. **Right-click** an empty space in the directory and **click** "Open Powershell" or "Open Terminal".
4. **Type** "Get-FileHash VirtualBox..." (**click** TAB to complete command), then **click** ENTER.
5. Copy the hash that was computed.
6. Go back to the SHA256 checksums list and **click** Ctrl-F to open the search bar.
7. **Paste** in the computed checksum.
- If the computed checksum is found in the SHA256 checksums list, then the file has not been altered.
- If the computed checksum is NOT found in the SHA256 checksums list, 
  then the file has been altered. Delete that file, navigate directly 
  to virtualbox.org, re-download, and verify the checksum again before running it.

### Separate Steps Taken for Kali Linux
1. Downloaded the prebuilt VirtualBox image from kali.org.
2. Unzipped download package.
3. Inside the unzipped folder, **Double-clicked** the "VirtualBox Machine Definition" file - it opens up in VirtualBox.

## Lessons Learned
This lab was only my second experience with setting up virtual machines, and just like the first time, I've learned a lot!
The difference with this time though is that now, I will be managing 4 different VMs - with some even running on different OSs, and all just on one physical machine.
This time around with installing this specific lab environment, it mimicks exactly how some enterprise environments simulate isolated networks in test labs.

By adding this project to my personal studies, I get to work closer with the lab environment and dive deeper into understanding the specific functions and specifications of each machine and why they matter.
Moreover, learning about different network adapter settings was the main takeaway here. The Windows 10 Target Client uses Internal Network because it's supposed to only communicate through the Domain Controller and not directly to the internet.
Windows Server 2022 and Splunk use NAT so that similarly they're protected from external access but they also still have the ability to reach the internet when needed. 

Additional to these precautions, I took snapshots after each clean install to have something to fallback to in case the environment faces issues (e.g., misconfigurations) or threats (e.g., malware) and I will continue to use/create snapshots during this project.
Overall, by having this mindset of creating checkpoints/save states, I'll develop a healthy habit of protecting IT operations and security work, which is extremely realistic for all organizational environments.
