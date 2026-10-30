Cybersecurity Lab Environment Setup

Building an isolated virtual lab for penetration testing and ethical hacking practice.

Load image
📌 Project Overview
This project sets up a virtual cybersecurity and penetration-testing lab using VirtualBox and Kali Linux, as part of the Networkwalks Cybersecurity Program (Week 1, Project Module 1).
The lab runs on a private virtual network so more machines can be added later and used as targets for authorized security testing. Everything is done on my own laptop, in my own lab environment.
Project Requirements
Use VirtualBox as the base
Set up Kali Linux as the attacker machine
Create a NAT Network in subnet 10.0.0.0/24
Enable shared clipboard and drag & drop
Share the host Downloads folder with the VM
Set the Kali IP address to 10.0.0.2/24
Give Kali full Internet access
Tools & Environment
Item
Details
Host OS
Windows 11 (Dell laptop)
Hypervisor
Oracle VirtualBox 7.2
Guest OS
Kali Linux 2026.3 (pre-built VirtualBox image)
Archive tool
7-Zip
Network type
NAT Network
Subnet
10.0.0.0/24
Kali IP
10.0.0.2/24
Gateway
10.0.0.1
DNS
8.8.8.8
Lab Architecture
Windows 11 Host (VirtualBox)
        |
   NatNetwork (10.0.0.0/24)
        |
   Kali Linux  -  10.0.0.2/24
Other virtual machines (Windows, Android, etc.) can join the same NatNetwork later.
 Steps Performed
Step 1: Install 7-Zip and VirtualBox
I downloaded and installed 7-Zip (to extract the Kali archive) and the latest VirtualBox 7.2 build. Both installers can be seen in my Downloads folder in the screenshot below (Step 3).
Step 2: Create the NAT Network
In VirtualBox: File > Tools > Network > NAT Networks > Create.
Name: NatNetwork
IPv4 Prefix: 10.0.0.0/24
DHCP: Enabled
IPv6: Disabled
NAT Network shown in the VirtualBox network manager:
I also created and verified the NAT Network from the command line with VBoxManage:
VBoxManage natnetwork add --netname NatNetwork --network "10.0.0.0/24" --enable --dhcp on
VBoxManage natnetwork list
Step 3: Download and Import Kali Linux
I downloaded the pre-built Kali Linux VirtualBox image from the official Kali website, extracted it, and added the virtual machine to VirtualBox.
Step 4: Configure the VM Settings
Network: Adapter 1 attached to NAT Network (NatNetwork), Promiscuous Mode set to Allow All
General > Advanced: Shared Clipboard and Drag'n'Drop set to Bidirectional
Shared Folders: host Downloads folder added with Auto-mount and Full access
📸 Screenshot to add: VM Settings > Network (Adapter 1 on NAT Network)
📸 Screenshot to add: VM Settings > General > Advanced (Clipboard and Drag'n'Drop)
📸 Screenshot to add: VM Settings > Shared Folders
Kali is running and the shared folder appears on the desktop as sf_Downloads, which confirms the shared folder works:
Load image
Step 5: Set the Static IP on Kali
I configured the static IP using nmcli in the terminal:
sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.addresses 10.0.0.2/24 ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
My first attempts failed (wrong sudo password typed, and a mistyped property name), as shown below. See the Problems section.
Load image
📸 Screenshot to add: ip a showing inet 10.0.0.2/24 on eth0 after the fix
Step 6: Test Internet Access
ip a
ping -c 4 8.8.8.8
ping -c 4 google.com
First test result, before the fix: eth0 had no IPv4 address and the ping failed with "Network is unreachable".
Load image
📸 Screenshot to add: successful ping -c 4 8.8.8.8 and ping -c 4 google.com after the fix
Step 7: Take a Snapshot
Machine > Take Snapshot, named Kali-Fresh-10.0.0.2, to keep a clean restore point before testing.
📸 Screenshot to add: Snapshots tab showing Kali-Fresh-10.0.0.2
⚠️ Problems Faced & Solutions
Problem 1: Host PC had no Internet
Kali reaches the Internet through the host machine, so with no Internet on my laptop the VM had none either.
Solution: Connect the laptop to Wi-Fi or a phone hotspot first, confirm Internet works in Windows, then test again from Kali.
Problem 2: nmcli command errors
The command failed with Error: invalid <setting>.<property> 'ipv4', because I mistyped the property name (it must be ipv4.addresses). I also typed the sudo password wrongly three times.
Solution: Type the command carefully (use ipv4.addresses, not ipv4), start it with sudo, and enter the correct password.
Problem 3: ping: connect: Network is unreachable
ip a showed eth0 UP but with no IPv4 address, so Kali had no route to the Internet.
Solution: Apply the static IP with the corrected nmcli commands, then bring the connection down and up. If Internet still fails on Kali 2026.x with VirtualBox 7, disable duplicate address detection and restart:
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
Problem 4: Two NAT Networks with the same subnet
I accidentally had NatNetwork1 and NatNetwork, both using 10.0.0.0/24, which can cause conflicts.
Solution: Keep only one NAT Network (NatNetwork), remove the extra one, and make sure the VM adapter uses NatNetwork.
📚 What I Learned
How to build an isolated virtual lab with VirtualBox
How a NAT Network lets several VMs share Internet access and talk to each other
How to configure a static IPv4 address on Kali Linux with nmcli
How shared folders, clipboard and drag & drop connect the host and the VM
How to troubleshoot network problems step by step (host first, then VM, IP, DNS)
How to read ip a and ping output to find where the problem is
✅ Result
Kali Linux runs in VirtualBox on a private NAT Network (10.0.0.0/24), with the shared folder working. Static IP 10.0.0.2/24 and Internet access: (update this line after your final test).
⚖️ Disclaimer
This lab is for education and learning only. All testing is done on my own devices and my own virtual lab environment. Hacking without written permission from the owner is illegal.
Project: Networkwalks Cybersecurity Program, Week 1 (WK1-PM1)
