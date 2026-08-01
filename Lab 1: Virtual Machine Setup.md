# Lab 1: Windows Server 2022 Setup & Active Directory Installation

## Overview
<img width="1275" height="683" alt="Screenshot 2026-07-31 233839" src="https://github.com/user-attachments/assets/b80e26ef-04e0-4e86-ba2c-6d08b8a5065c" />

The goal of this lab was to set up the Windows Server 2022 virtual machine inside of VirtualBox that will serve as the Domain Controller for my Active Directory home lab environment. I downloaded the ISO and completed the initial setup of the VM before moving on to installing Active Directory Domain Services (AD DS).

- **Domain Controller:** Windows Server 2022 (from Microsoft Evaluation Center)

## Specs

**Windows Server 2022 - Domain Controller:**
- Type: Microsoft Windows/Windows 2022 (64-bit)
- RAM: 2048 MB
- CPUs: 1
- Disk: 50 GB
- Network: NAT (keeps the VM isolated from external exposure while still allowing internet access)

## Steps Taken

### Creating the Virtual Machine & Installing Windows Server 2022

1. Opened up VirtualBox and clicked **New**.
2. Named the VM and selected the correct OS type & version.
3. Attached the Windows Server 2022 ISO.
4. Set the RAM, CPU, and disk size specs.
5. Booted the VM and completed the OS installation.
6. Took a snapshot after the clean install and labeled it "Fresh Install" to have a baseline to refer/revert to if necessary.

### Installing Active Directory

1. Opened Server Manager and went to **Manage > Add Roles and Features**.
2. Selected "Role-based or feature-based installation" and continued through the wizard.
3. Selected the **Active Directory Domain Services** role, along with a handful of other roles I'll want for future labs (DNS Server, DHCP Server, Remote Access, Print and Document Services, Windows Server Update Services, and Network Policy and Access Services).
4. Made sure **Group Policy Management** was included in the features window.
5. Clicked through the remaining prompts and selected **Install**.
6. Once AD DS finished installing, clicked **Promote this server to a domain controller**.
7. Chose to add a new forest and entered a domain name, using ".local" since this is a local/private domain.
8. Selected the Windows Server 2016 functional level and set the Directory Services Restore Mode password.
9. Clicked through the remaining prompts and installed. The server rebooted to complete the domain controller promotion.

### Basic Active Directory Setup

1. After the reboot, logged back in and opened **Active Directory Users and Computers** from the search bar.
2. Right-clicked the domain and selected **New > Organizational Unit** to create OUs for each region: USA, Europe, and Asia.
3. Within each regional OU, created additional OUs to organize Computers, Users, and Servers.
4. Created groups for each department (IT, Accounting, Sales, Management) under the Users sub-OU within each regional OU (e.g., USA > Users > IT).
5. Created 3 users for each department group.

## Security Tip with Downloading VirtualBox

When downloading VirtualBox, use the SHA256 checksums to verify that the download has not been altered:

1. Download the SHA256 checksums file from VirtualBox.
2. Go to your local Downloads directory.
3. Right-click an empty space in the directory and click "Open Powershell" or "Open Terminal".
4. Type `Get-FileHash VirtualBox...` (click TAB to complete command), then click ENTER.
5. Copy the hash that was computed.
6. Go back to the SHA256 checksums list and click Ctrl-F to open the search bar.
7. Paste in the computed checksum.
8. If the computed checksum is found in the SHA256 checksums list, then the file has not been altered.
9. If the computed checksum is NOT found in the SHA256 checksums list, then the file has been altered. Delete that file, navigate directly to virtualbox.org, re-download, and verify the checksum again before running it.

## Lessons Learned

This lab was only my second experience with setting up virtual machines, and just like the first time, I learned a lot! Setting up the Domain Controller and installing Active Directory on it mimics exactly how some enterprise environments simulate isolated networks in test labs.

By adding this project to my personal studies, I got to work closer with the lab environment and dive deeper into understanding the specific functions and specifications of the Domain Controller and why they matter.

Going through the Add Roles and Features wizard also taught me that it's worth thinking ahead about which roles I'll actually need instead of just installing AD DS and stopping there. I added DNS, DHCP, Print and Document Services, Network Policy and Access Services, and Remote Access alongside AD DS so I'll have those roles ready for future labs, but I held off on Remote Desktop Services and WSUS since those have their own setup process that deserves its own lab. I also learned that Hyper-V would technically let me run VMs inside this VM, but decided against enabling it since nested virtualization on top of VirtualBox adds a lot of complexity for little benefit in this particular environment I have setup.

Once AD was actually installed, building out the OU structure gave me a much better sense of how organizations actually lay out their directory. Splitting things out by region first (USA, Europe, Asia) and then by department (IT, Accounting, Sales, Management) within each region made it click why OU design matters, since it directly affects how Group Policy and permissions can be scoped later on. Creating the groups and adding users to them was also a good reminder of the difference between an OU and a group: OUs are for organizing and applying policy, while groups are for actually assigning permissions and access.

Additional to these precautions, I took a snapshot after the clean install to have something to fall back to in case the environment faces issues (e.g., misconfigurations) or threats (e.g., malware), and I will continue to use/create snapshots throughout this project. So overall, by having this mindset of creating checkpoints/save states, I'll develop a healthy habit of protecting IT operations and security work, which is extremely realistic for all organizational environments.
