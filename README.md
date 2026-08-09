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

## ARP Analysis with Wireshark
ARP traffic between Kali Linux and the Ubuntu Server was captured and analyzed using Wireshark.

When Kali Linux needed to communicate with 192.168.100.20, ARP was used to resolve the IP address to the corresponding MAC address.

The ARP request was sent as an Ethernet broadcast because the destination MAC address was not yet known. Ubuntu then responded with an ARP reply containing its MAC address.

This demonstrated how ARP maps IPv4 addresses to MAC addresses within the local Layer 2 network.

<img width="1005" height="563" alt="Wireshark ARP" src="https://github.com/user-attachments/assets/4905ea2d-8620-46b6-a656-7fa24005e06d" />
## TCP & SSH Analysis with Wireshark
SSH traffic between Kali Linux and the Ubuntu Server was captured and analyzed with Wireshark.

The TCP three-way handshake was observed before the SSH session was established:

1. SYN – Kali initiates the TCP connection to port 22.
2. SYN-ACK – Ubuntu acknowledges the request and synchronizes its own sequence number.
3. ACK – Kali acknowledges Ubuntu's response and the TCP connection is established.

TCP sequence and acknowledgment numbers were also analyzed. Sequence numbers identify positions within the TCP byte stream, while acknowledgment numbers indicate the next sequence number expected by the receiver.

The TCP connection termination using FIN and ACK packets was also observed.

<img width="1003" height="765" alt="Wireshark TCP 1" src="https://github.com/user-attachments/assets/6e1dcd67-6956-436d-8fc6-91ae2d1c928b" />
## Basic Network Troubleshooting

Basic network troubleshooting was performed from Kali Linux to identify connectivity and service-related issues.

The following tools were used:

- `ip addr` to verify interface and IP configuration
- `ip route` to inspect the routing table and determine which interface is used
- `ping` to verify IP connectivity to the Ubuntu Server
- `nmap` to check the state of TCP ports

Different Nmap port states were tested:

- `open` – the service is listening and reachable
- `closed` – the host is reachable, but no service is listening on the port
- `filtered` – traffic is being filtered, for example by a firewall

A firewall rule was temporarily applied to TCP port 22, changing the Nmap result from `open` to `filtered`. After removing the test configuration, SSH connectivity was restored.

<img width="639" height="469" alt="Firewall port 22 rules" src="https://github.com/user-attachments/assets/d7efd305-a1f3-4c3f-8188-be5dc4c8867b" />

## SSH Hardening and Firewall Configuration 

## SSH Key-Based Authentication
SSH key-based authentication was configured between the Kali Linux client
(192.168.100.10) and the Ubuntu server (192.168.100.20).

Password authentication was disabled and public key authentication was enabled.

Verified SSH configuration:

- `PermitRootLogin no`
- `PasswordAuthentication no`
- `PubkeyAuthentication yes`

A connection attempt without public key authentication was rejected with:

`Permission denied (publickey).`

A normal SSH connection using the configured Ed25519 key was successful.



<img width="645" height="636" alt="new ssh login configs pubkey password" src="https://github.com/user-attachments/assets/5bbf1206-f8f9-48e4-bbc9-facf5008fa4e" />


<img width="798" height="650" alt="key login succesful" src="https://github.com/user-attachments/assets/a3d99e08-dfb1-4cae-bf73-eeb37b661cbd" />

### UFW Firewall Configuration

UFW was enabled on the Ubuntu server with a default-deny policy for incoming traffic.

SSH access was restricted to the Kali Linux host:

- Kali Linux: `192.168.100.10`
- Ubuntu Server: `192.168.100.20`
- Allowed service: SSH (`TCP/22`)

Firewall rule:

`ALLOW IN 192.168.100.10 -> TCP/22`

This ensures that SSH remains reachable from the authorized Kali host while other unsolicited incoming connections are blocked.

<img width="641" height="345" alt="linux firewall rules allow only pakets from kali" src="https://github.com/user-attachments/assets/79f4a126-b3a3-4bb5-8c0b-fe6bf4ec2723" />


### Firewall Testing with Nmap

The firewall behavior was tested from Kali Linux using an Nmap SYN scan.

Port 22 was reachable because it was explicitly allowed by the firewall:

`22/tcp open ssh`

A scan against TCP port 8080 returned:

`8080/tcp filtered`

The `filtered` state indicates that the firewall prevented Nmap from receiving the normal TCP response required to determine whether the port was open or closed.

<img width="640" height="247" alt="nmap kali port 8080 filtered " src="https://github.com/user-attachments/assets/e835ff1f-5a98-499a-a5a3-b27cbae8dffd" />

<img width="1016" height="806" alt="nmap port 22 open allow pakets " src="https://github.com/user-attachments/assets/06f12276-9430-46a6-878f-1e97a9058297" />

## Next Steps

The next steps for this homelab are:
- Analyze firewall logs and blocked network traffic
- Deploy and secure additional services on the Ubuntu server
- Expand the lab with additional network services and configurations
- Continue practicing network troubleshooting and traffic analysis

