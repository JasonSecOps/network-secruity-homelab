# network-secruity-homelab
My personal cybersecruity homelab for networking, Linux and secruity practice 
## Overview
This repository documents my personal secruity homelab.
The goal of this lab is to build practical skills in networking, Linux administration and cybersecruity by creating and troubleshooting my own virtual network environment.
The lab currently consists of two Ubuntu systems and one Kali Linux machine running as virtual machines on Hyper-V.
## Lab Architecture
The homelab runs on a Windows Host using Hyper-V for virtualization
## Virtual Maschines
- **Ubuntu Server** – Target/server system
- **Ubuntu 2** – Additional Linux client for network and access-control testing
- **Kali Linux** – Security and administration workstation

## Virtual Networking
All three virtual machines use two virtual network adapters:
- **Lab network adapter** – Used for direct communication between the virtual machines
- **Default Switch** – Provides Internet access to the virtual machines

## Network Configuration
The lab network uses the private subnet:

192.168.100.0/24

Static IP addresses are assigned to the lab interfaces:

| System | Lab IP |
|---|---|
| Kali Linux | 192.168.100.10 |
| Ubuntu Server | 192.168.100.20 |
| Ubuntu 2 | 192.168.100.30 |

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

### Testing Firewall Behavior with a Temporary HTTP Server

To better understand the difference between an open port, a closed port, and firewall filtering, a temporary HTTP server was started on the Ubuntu server using Python.

The temporary HTTP server was started on TCP port 8080:

```bash
python3 -m http.server 8080
```

The port was then tested from Kali Linux using Nmap:

```bash
nmap -p 8080 192.168.100.20
```

During the experiment, different port states could be observed depending on the firewall configuration and whether a service was actually listening on the port.

- **filtered** – Network traffic is being blocked or dropped by the firewall.
- **closed** – The host is reachable, but no service is currently listening on the port.
- **open** – A service is listening on the port and the firewall allows the connection.

This experiment demonstrated an important distinction between firewall rules and services: allowing TCP port 8080 through the firewall does not automatically make the port open. A service must also be actively listening on that port.

<img width="615" height="202" alt="nmap port 8080 reject" src="https://github.com/user-attachments/assets/d96f3f40-5ff2-4f7f-a853-33230eb459f0" />

<img width="557" height="189" alt="python3 nmap webserver port open" src="https://github.com/user-attachments/assets/c331a2a8-d26a-44f9-8bf8-d9e8697522d4" />

This experiment demonstrated an important distinction between firewall rules and services: allowing TCP port 8080 through the firewall does not automatically make the port open. A service must also be actively listening on that port.
### Adding a Second Ubuntu Client and Testing Subnet-Based SSH Access

A second Ubuntu virtual machine was added to the lab to test firewall rules with multiple hosts.

The new system was assigned the following LabNet address:

```text
Ubuntu 2: 192.168.100.30/24
```

The existing systems are:

```text
Kali Linux: 192.168.100.10
Ubuntu Server: 192.168.100.20
Ubuntu 2: 192.168.100.30
```

Connectivity from Ubuntu 2 to the Ubuntu server was verified using ICMP:

```bash
ping 192.168.100.20
```

<img width="587" height="179" alt="icmp verify" src="https://github.com/user-attachments/assets/ff682dff-a161-4a70-87d2-4339a360eed1" />


The Ubuntu server was configured with a UFW rule allowing SSH access from the entire LabNet subnet:

```bash
sudo ufw allow from 192.168.100.0/24 to any port 22 proto tcp
```

This allows all hosts inside `192.168.100.0/24` to reach TCP port 22 on the Ubuntu server.

Ubuntu 2 initially reached the SSH service, but authentication failed because its public key had not yet been added to the server:

```text
Permission denied (publickey)
```

A new ED25519 SSH key pair was created on Ubuntu 2:

```bash
ssh-keygen
```

The public key was then transferred to Kali Linux using a temporary Python HTTP server.

On Ubuntu 2:

```bash
python3 -m http.server 8080
```

<img width="637" height="185" alt="ssh private key config " src="https://github.com/user-attachments/assets/a97e5cd0-4a98-452b-af78-8f54d1a9df26" />



On Kali Linux:

```bash
wget http://192.168.100.30:8080/id_ed25519.pub
```

<img width="623" height="185" alt="ssh public key config " src="https://github.com/user-attachments/assets/f058096a-35b7-4a19-9018-8beff87deda5" />





The downloaded public key was then appended to the Ubuntu server's `authorized_keys` file through Kali's existing SSH access:

```bash
cat id_ed25519.pub | ssh jasonsecops@192.168.100.20 'cat >> ~/.ssh/authorized_keys'
```
<img width="623" height="217" alt="ssh private key succesful on ubuntu 2 " src="https://github.com/user-attachments/assets/0be410ec-f1b4-4047-ab17-19f8288741cf" />

After adding the key, Ubuntu 2 successfully authenticated to the Ubuntu server using SSH:

```bash
ssh jasonsecops@192.168.100.20
```

The active SSH connection was verified with:

```bash
echo $SSH_CONNECTION
```

<img width="522" height="43" alt="SSH connection ports" src="https://github.com/user-attachments/assets/6edf302c-addb-48ac-9925-e7b724f46b3c" />

Example result:

```text
192.168.100.30 43626 192.168.100.20 22
```

This confirms that Ubuntu 2 (`192.168.100.30`) established an SSH connection to the Ubuntu server (`192.168.100.20`) on TCP port 22.

<img width="645" height="487" alt="connection done" src="https://github.com/user-attachments/assets/3e0dee49-5b48-4363-9c88-a4f180c51d6a" />

### Comparing Firewall DROP and REJECT Behavior

Different firewall responses were tested to understand how blocked TCP connections appear during network analysis.

When traffic is silently dropped by the firewall, the sender does not receive a TCP response. Nmap therefore reports the port as:

```text
filtered
```

This behavior makes it difficult for the scanning host to determine whether a service exists behind the firewall.

A firewall can also explicitly reject a TCP connection instead of silently dropping it. In this case, the receiving system actively informs the sender that the connection is not accepted.

During packet analysis, a TCP packet containing the `RST, ACK` flags was observed.

```text
RST, ACK
```

<img width="960" height="678" alt="Wireshark port 8080 RST" src="https://github.com/user-attachments/assets/782b8cd7-41b6-407a-931a-fb508288018c" />

`RST` resets the TCP connection, while `ACK` acknowledges the previously received TCP segment.

This demonstrates an important difference between the two firewall behaviors:

- **DROP** — silently discards the packet and normally sends no response.
- **REJECT** — refuses the connection and actively returns a response.

From a security perspective, DROP reveals less information to an external scanner, while REJECT provides faster and clearer feedback that the connection is not permitted.


## SSH Authentication Monitoring and Fail2ban

SSH authentication activity was monitored on the Ubuntu server to observe failed login attempts and understand how authentication events are recorded.

### Monitoring SSH Authentication Attempts

SSH authentication events were inspected using the system logs. Failed SSH login attempts from the second Ubuntu client (`192.168.100.30`) were generated intentionally in the isolated lab environment.

The logs showed the source IP address and the failed authentication attempts, demonstrating how SSH activity can be monitored and investigated.

### Fail2ban SSH Protection

Fail2ban was installed and configured to monitor the SSH service.

The `sshd` jail was enabled with the following relevant settings:

- `maxretry = 5`
- `findtime = 600`
- `bantime = 600`

This configuration allows five failed authentication attempts within a 10-minute window before the source IP address is temporarily banned for 10 minutes.

### Testing Automatic Banning

Multiple failed SSH authentication attempts were generated from Ubuntu 2:

`192.168.100.30`

<img width="619" height="216" alt="SSH fail2ban 1" src="https://github.com/user-attachments/assets/a45d18e0-3134-4840-a32a-812846e9cb21" />

After the configured threshold was reached, Fail2ban detected the repeated failures and banned the source address.

The Fail2ban log confirmed the action:

`[sshd] Ban 192.168.100.30`

<img width="661" height="218" alt="ssh fail2ban 3 list completly" src="https://github.com/user-attachments/assets/42d4feff-562c-4b13-96e6-66694629a516" />

### Firewall Enforcement

The active Fail2ban firewall rules were inspected using nftables.

The Fail2ban address set contained:

`192.168.100.30`

Traffic from this address to TCP port 22 was rejected by the dynamically created Fail2ban rule.

This demonstrates the complete detection and response process:

Failed SSH authentication
→ log entry
→ Fail2ban detection
→ retry threshold reached
→ source IP added to ban set
→ SSH traffic blocked by the firewall

<img width="937" height="325" alt="ssh fail2ban 4 list " src="https://github.com/user-attachments/assets/0c2039b8-a67b-4c79-abb9-27ce616b7156" />

After the ban expired, SSH access from Ubuntu 2 was restored automatically.

## Monitoring with Prometheus and Grafana

To extend the homelab beyond network security and access control, a monitoring stack was implemented on the Ubuntu server.

The monitoring architecture consists of three main components:

- **Node Exporter** – collects system-level metrics from the Ubuntu server
- **Prometheus** – periodically collects and stores the metrics provided by Node Exporter
- **Grafana** – queries Prometheus and visualizes the collected metrics in dashboards

The resulting monitoring flow is:

```text
Ubuntu Server
│
▼
Node Exporter (:9100)
│
▼
Prometheus (:9090)
│
▼
Grafana (:3000)
│
▼
Monitoring Dashboard
```

### Node Exporter

Prometheus Node Exporter was installed on the Ubuntu server to expose operating system metrics such as CPU usage, memory utilization, disk activity, filesystem usage, and network statistics.

The Node Exporter service exposes these metrics through an HTTP endpoint on TCP port `9100`.

The endpoint was manually verified using:

```bash
curl http://localhost:9100/metrics
```

This confirmed that Node Exporter was successfully collecting and exposing system metrics.

<img width="637" height="302" alt="Metrikenprüfung" src="https://github.com/user-attachments/assets/27e0c4c1-929d-4c87-a3ba-40bc08e58a2e" />


### Prometheus

Prometheus was installed as the central metrics collection and storage service.

The configuration contains two monitoring targets:

```yaml
- job_name: prometheus
static_configs:
- targets: ['localhost:9090']

- job_name: node
static_configs:
- targets: ['localhost:9100']
```

The `prometheus` target allows Prometheus to monitor its own operational metrics, while the `node` target retrieves system metrics from Node Exporter.

Prometheus periodically performs HTTP requests to the `/metrics` endpoints and stores the resulting values as time-series data.

The Prometheus Targets interface confirmed that both configured targets were successfully reachable:

- `localhost:9090` – Prometheus
- `localhost:9100` – Node Exporter

Both targets reported the state **UP**.

<img width="1005" height="638" alt="Prometheus targets" src="https://github.com/user-attachments/assets/e1d17199-ccee-4025-8b46-a92f21ad3cdd" />


### Firewall Configuration

Prometheus listens on TCP port `9090`.

Because UFW is enabled on the Ubuntu server, remote access to the Prometheus web interface was initially blocked.

Access was explicitly restricted to the Kali Linux host:

```bash
sudo ufw allow from 192.168.100.10 to any port 9090 proto tcp
```

This allows only the Kali host (`192.168.100.10`) to access the Prometheus web interface over the lab network.

This demonstrates the difference between a service **listening on a network port** and that service actually being **reachable through the host firewall**.

### Grafana Installation

Grafana was installed using the official Grafana APT repository.

The repository signing key was stored under:

```text
/etc/apt/keyrings/grafana.gpg
```

APT uses this key to verify the authenticity and integrity of packages obtained from the Grafana repository.

After adding the repository, Grafana was installed through the system package manager and started as a systemd service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now grafana-server
```

The service status confirmed that Grafana was successfully running.

<img width="635" height="412" alt="grafana succesfull running" src="https://github.com/user-attachments/assets/7e0f5eae-de78-4507-b43f-822c3eb8144a" />


Grafana provides its web interface on TCP port `3000`.

Access from the Kali Linux host was permitted through UFW:

```bash
sudo ufw allow from 192.168.100.10 to any port 3000 proto tcp
```

The Grafana interface could then be accessed from Kali at:

```text
http://192.168.100.20:3000
```

### Prometheus Data Source

Prometheus was configured as the Grafana data source using:

```text
http://localhost:9090
```

`localhost` can be used because Grafana and Prometheus are running on the same Ubuntu server.

The resulting data flow is:

```text
Node Exporter
│
▼
Prometheus
│
▼
Grafana
│
▼
Dashboard
```

Grafana does not directly collect the operating system metrics. Instead, it queries the metrics already collected and stored by Prometheus.

### Node Exporter Dashboard

The **Node Exporter Full** Grafana dashboard was imported to visualize the collected metrics.

The dashboard provides visibility into several areas of the Ubuntu server, including:

- CPU utilization
- Memory and swap usage
- Filesystem utilization
- Disk I/O
- Network traffic
- System load
- System processes
- Systemd services
- Network sockets
- System uptime

<img width="1001" height="734" alt="grafana prometheus dashboard" src="https://github.com/user-attachments/assets/ec41bbdd-323c-485f-9426-6f473507a4af" />


The dashboard confirms that the complete monitoring pipeline is operational:

```text
Ubuntu → Node Exporter → Prometheus → Grafana
```

### Monitoring Verification

To verify that changes in system activity are detected by the monitoring stack, CPU load was intentionally generated on the Ubuntu server:

```bash
yes > /dev/null
```

The command continuously generates output and redirects it to `/dev/null`, causing CPU activity without filling the terminal with output.

The resulting increase in CPU utilization could be observed through the Grafana dashboard, confirming that system activity is successfully collected by Node Exporter, stored by Prometheus, and visualized by Grafana.

The test process was terminated using `Ctrl+C`.

> **Note:** A detailed CPU load graph will be added after further monitoring tests.

## Security Log Monitoring with Loki and Grafana Alloy

The monitoring environment was extended to include centralized security log collection and analysis.

While Prometheus is used for numerical system metrics, Loki was added as a log aggregation backend. Grafana Alloy was configured as the collector responsible for reading security logs and forwarding them to Loki.

The resulting log monitoring architecture is:

```text
SSH / systemd journal ────────┐
│
▼
Grafana Alloy
│
▼
Loki
│
▼
Grafana
▲
│
/var/log/fail2ban.log ────────┘
```

### Loki

Loki was installed on the Ubuntu server and runs as a systemd service.

Loki listens locally on TCP port `3100`.

Because Loki, Alloy, and Grafana are running on the same Ubuntu server, communication between these components can use `localhost`. Therefore, Loki does not need to be exposed directly to the lab network.

Loki readiness was verified with:

```bash
curl http://localhost:3100/ready
```

A successful response returned:

```text
ready
```

<img width="632" height="63" alt="loki ready" src="https://github.com/user-attachments/assets/8aedc6cd-3aeb-47ba-8de9-18ca553c85ff" />


### Grafana Alloy

Grafana Alloy was installed as the telemetry collector for the logging pipeline.

Alloy runs as its own Linux service account. The service account has access to the systemd journal, allowing Alloy to collect authentication events generated by SSH.

The Alloy configuration is stored in:

```text
/etc/alloy/config.alloy
```

Before modifying the configuration, a backup was created:

```bash
sudo cp /etc/alloy/config.alloy /etc/alloy/config.alloy.bak
```

### SSH Log Collection

SSH authentication events are written to the systemd journal.

Alloy was configured to:

- Read events generated by `ssh.service`
- Label these events with `job="ssh"`
- Forward the logs to Loki

The following configuration was added:

```alloy
loki.source.journal "ssh" {
matches = "_SYSTEMD_UNIT=ssh.service"

labels = {
job = "ssh",
}

forward_to = [loki.write.local.receiver]
}

loki.write "local" {
endpoint {
url = "http://localhost:3100/loki/api/v1/push"
}
}
```

<img width="608" height="259" alt="alloy configuration for loki" src="https://github.com/user-attachments/assets/9d16c6b4-cb71-4b64-9906-12e986578f29" />


This creates the following log flow:

```text
sshd
↓
systemd journal
↓
Grafana Alloy
↓
Loki
```

Before loading the configuration, it was validated with:

```bash
sudo alloy validate /etc/alloy/config.alloy
```

After successful validation, Alloy was reloaded:

```bash
sudo systemctl reload alloy
```

A new SSH connection from Kali Linux (`192.168.100.10`) to the Ubuntu server (`192.168.100.20`) was then generated.

The resulting authentication event was successfully collected by Alloy and stored in Loki.

One of the captured events showed successful SSH public-key authentication:

```text
Accepted publickey for jasonsecops from 192.168.100.10
```

### Loki Data Source in Grafana

Loki was added as an additional Grafana data source.

Because Grafana and Loki run on the same Ubuntu server, the following local address was used:

```text
http://localhost:3100
```

Grafana successfully established a connection to Loki.

<img width="1007" height="684" alt="grafana loki succesfull" src="https://github.com/user-attachments/assets/2c316cf3-494f-47ed-b758-5aabc61b28ab" />


SSH logs were then queried through Grafana Explore using LogQL:

```logql
{job="ssh"}
```

The query selects all log streams that were labeled by Alloy with:

```text
job="ssh"
```

The SSH authentication event generated from Kali Linux was successfully displayed in Grafana.

<img width="1011" height="696" alt="Grafana Loki logs" src="https://github.com/user-attachments/assets/e6f04e8a-4a5c-465f-a3f4-fb608a48a6bc" />


This confirmed the complete SSH logging pipeline:

```text
Kali Linux
↓
SSH connection
↓
Ubuntu sshd
↓
systemd journal
↓
Grafana Alloy
↓
Loki
↓
Grafana
```

### Fail2Ban Log Collection

Fail2Ban uses its own log file:

```text
/var/log/fail2ban.log
```

Unlike the SSH logs, these events are collected directly from a log file instead of the systemd journal.

Before configuring the log source, access to the file from the Alloy service account was verified with:

```bash
sudo -u alloy head -n 5 /var/log/fail2ban.log
```

This confirmed that the Alloy service account had permission to read the Fail2Ban log.

Alloy was then extended with the following configuration:

```alloy
local.file_match "fail2ban" {
path_targets = [{
"__path__" = "/var/log/fail2ban.log",
"job" = "fail2ban",
}]
}

loki.source.file "fail2ban" {
targets = local.file_match.fail2ban.targets
forward_to = [loki.write.local.receiver]
}
```

<!-- SCREENSHOT PLACEHOLDER: Alloy Fail2Ban configuration in /etc/alloy/config.alloy -->

The `local.file_match` component identifies the Fail2Ban log file and assigns the label:

```text
job="fail2ban"
```

The `loki.source.file` component reads the file and forwards new log entries to the existing Loki writer.

The resulting pipeline is:

```text
Fail2Ban
↓
/var/log/fail2ban.log
↓
Grafana Alloy
↓
Loki
↓
Grafana
```

After modifying the Alloy configuration, it was validated again:

```bash
sudo alloy validate /etc/alloy/config.alloy
```

The running Alloy service was then reloaded:

```bash
sudo systemctl reload alloy
```

### Fail2Ban Logs in Grafana

Fail2Ban events were queried in Grafana Explore using:

```logql
{job="fail2ban"}
```

This successfully displayed entries collected from:

```text
/var/log/fail2ban.log
```

<img width="777" height="611" alt="loki fail2ban logs" src="https://github.com/user-attachments/assets/a065df51-08b8-4bf5-84c5-e095bdbb5f8e" />



LogQL can also filter the returned log lines.

For example:

```logql
{job="fail2ban"} |= "Ban"
```

The `|=` operator filters the selected log stream and only returns lines containing the specified text.

This can be used to locate Fail2Ban security events such as banned IP addresses.

### Monitoring Architecture

The homelab now contains two separate monitoring pipelines.

#### System Metrics

```text
Ubuntu
↓
Node Exporter
↓
Prometheus
↓
Grafana
```

Prometheus stores numerical time-series metrics such as:

- CPU utilization
- Memory usage
- Disk utilization
- Network activity

#### Security Logs

```text
SSH ────────────────┐
│
▼
Grafana Alloy
│
▼
Loki
│
▼
Grafana
▲
│
Fail2Ban ───────────┘
```

Loki stores searchable log events such as:

- SSH authentication events
- Successful public-key logins
- Failed authentication attempts
- Fail2Ban events
- IP bans

### Result

The monitoring stack now provides both infrastructure metrics and centralized security logs.

```text
METRICS

Node Exporter
↓
Prometheus
↓
Grafana


LOGS

SSH / Fail2Ban
↓
Grafana Alloy
↓
Loki
↓
Grafana
```

This provides the foundation for building a dedicated Grafana security dashboard that combines SSH authentication activity, Fail2Ban events, and infrastructure monitoring in a single interface.

## Next Steps

- Configure Grafana alerts for CPU, memory and disk usage
- Monitor SSH authentication events and Fail2Ban activity
- Centralize and analyze system and security logs
- Generate controlled test activity from the Kali Linux VM
- Visualize security-relevant events in Grafana
- Expand monitoring to additional hosts and services

