# network-secruity-homelab
My personal cybersecruity homelab for networking, Linux and secruity practice 
## Overview
This repository documents my personal secruity homelab.
The goal of this lab is to build practical skills in networking, Linux administration and cybersecruity by creating and troubleshooting my own virtual network environment.
the lab currently consists of an Ubuntu Server and a Kali Linux machine running as virtual machines on Hyper-V
## Lab Architecture
The homelab runs on a Windows Host using Hyper-V for virtualization
## Virtual Maschines
- Ubuntu Server - target/server system
- Kali Linux - secruity and administration workstation
## Virtual Networking
Both virtual maschines use two virtual network adapters:
- Lab network adapter - used for direct communication between Kali Linux and Ubuntu
- Default Switch - used to provide internet access to the virtual maschines
## Network Configuration
The lab network uses the private subnet:

192.168.100.0/24

Static IP addresses are assigned to the lab interfaces:

| System | Lab IP |
|---|---|
| Kali Linux | 192.168.100.10 |
| Ubuntu Server | 192.168.100.20 |

The lab interfaces are used for communication between the two virtual machines, while the second network adapters provide internet connectivity through the Hyper-V Default Switch.
## Troubleshooting
Initially, Kali Linux and Ubuntu Server were unable to communicate with each other over the lab network.

### Problem

The lab network configuration was not persistent on Ubuntu. The configured static IP address changed back to automatic configuration.

Additionally, the Hyper-V network adapters had to be assigned correctly so that the lab interface and the Default Switch were connected to the intended virtual network adapters.

### Solution

The Ubuntu lab interface was configured with the static IP address:

192.168.100.20/24

Kali Linux was configured with:

192.168.100.10/24

The Hyper-V network adapter configuration was then corrected so both virtual machines were connected to the same lab network.

### Verification

Connectivity was verified using ICMP ping between both systems.

After correcting the configuration, Kali Linux and Ubuntu Server were able to communicate successfully.
## SSH Setup
SSH was configured on the Ubuntu Server to allow remote administration from Kali Linux.

### SSH Server

The OpenSSH server was installed and enabled on Ubuntu.

SSH uses TCP port 22 by default.

### Connection

From Kali Linux, the Ubuntu Server can be accessed using:

ssh jasonsecops@192.168.100.20

The SSH connection was successfully established from Kali Linux to Ubuntu Server.

This allows the Ubuntu Server to be administered remotely from the Kali Linux terminal without directly interacting with the Ubuntu virtual machine.
## Next Steps
The next steps for this homelab are:

- Capture and analyze network traffic with Wireshark
- Practice basic network reconnaissance with Nmap
- Analyze TCP connections and common network protocols
- Add additional services to the Ubuntu Server
- Document future troubleshooting and configuration changes
