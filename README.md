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
<img width="1320" height="1760" alt="3943CAAF-D380-4AD7-B0DB-E87399F1DE22" src="https://github.com/user-attachments/assets/4694809a-c640-47bf-989c-8fa69a22c2e3" />
<img width="1320" height="1760" alt="14F1C5ED-17C3-425E-9C5B-70E87D64E173" src="https://github.com/user-attachments/assets/44d30677-72da-4153-8913-d53dde2c04d9" />

## Troubleshooting
Initially, Kali Linux and Ubuntu Server were unable to communicate with each other over the lab network.
<img width="1320" height="1760" alt="647B6EF2-D58A-40C9-BBB1-B577D56357D7" src="https://github.com/user-attachments/assets/0e5a1dff-e8bc-41e9-ae1b-79a9d87aa2cc" />

<img width="1320" height="1760" alt="398548D9-175D-40F1-A47B-9373E1B31070" src="https://github.com/user-attachments/assets/12b6a584-4d38-4bab-8de6-e6447f3ef897" />

### Problem

The lab network configuration was not persistent on Ubuntu. The configured static IP address changed back to automatic configuration.

Additionally, the Hyper-V network adapters had to be assigned correctly so that the lab interface and the Default Switch were connected to the intended virtual network adapters.

### Solution

The Ubuntu lab interface was configured with the static IP address:

192.168.100.20/24

Kali Linux was configured with:

192.168.100.10/24

The Hyper-V network adapter configuration was then corrected so both virtual machines were connected to the same lab network.
<img width="1320" height="1760" alt="D7930AA1-B9C0-4751-8D6F-3B8D70E387C8" src="https://github.com/user-attachments/assets/0dd52fa0-3904-441c-8cc7-d7e13c424b24" />

### Verification

Connectivity was verified using ICMP ping between both systems.

After correcting the configuration, Kali Linux and Ubuntu Server were able to communicate successfully.
## SSH Setup
SSH was configured on the Ubuntu Server to allow remote administration from Kali Linux.<img width="1320" height="1760" alt="D6DE0992-D2FD-42F6-9B87-656D94F04E04" src="https://github.com/user-attachments/assets/c53509be-1cb0-4064-aac1-58753c18f2bb" />

<img width="1320" height="1760" alt="D6DE0992-D2FD-42F6-9B87-656D94F04E04" src="https://github.com/user-attachments/assets/c3467268-5338-4636-96bd-321989853ed7" />

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
