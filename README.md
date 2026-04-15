# Linux Virtual Network Lab: DHCP & SSH Implementation

## 📌 Project Overview
This project demonstrates the configuration of a two-node virtual local area network (VLAN) using Linux VMs. I configured one node as a dedicated **DHCP and SSH Server** and the second as a **Client** to showcase automated network addressing and secure remote management.

Phase 1: Foundation (DHCP) –  I Established a private network segment. Configured the Linux VM as a DHCP server to automatically assign IP addresses to clients, ensuring basic network communication.
Phase 2: Remote Management (SSH) – Once the network was live, I hardened the server by installing OpenSSH. I transitioned from using the VM console to managing the server remotely, simulating a data centre environment.

## 🛠️ Tech Stack
*   **Operating System:** Linux Ubuntu 14.04LTS for server and Linux Ubuntu 18.06LTS for client 
*   **Services:** ISC-DHCP-Server, OpenSSH
*   **Network Config:** Static IP Addressing, Subnetting
*   **Virtualization:** Parallels 

## 📂 Repository Structure
*   `dhcpd.conf`: Configuration for the DHCP service and IP pools.
*   `sshd_config`: Security settings for remote SSH access.
*   `interfaces.conf`: Network interface configuration showing the Server's static IP.


## 🚀 Implementation Details

### 1. Went to the Virtual Machine settings for both linux VMs in Parallels and set the networking settings to host-only Network

### 2. I updated the local package repository and installed the required services on the Server VM:
```bash
sudo apt update && sudo apt install isc-dhcp-server openssh-server -y
```

### 3. Server Network Setup
I checked the network connection information to get the interface name for both the server and the client.
```bash
ip addr
```
Then to ensure the DHCP server remains reachable, I assigned it a **Static IP**. 
*   **Configuration File:** `interfaces`
*   **Settings:** IP `10.37.129.8` | Mask `255.255.255.0`
Then restarted the networking service so the changes to the interface file took effect.
```bash
sudo system restart networking
```

### 4. DHCP Configuration
I configured the `isc-dhcp-server` to manage a specific range of IPs, ensuring it does not conflict with the server's static address.
*   **Configuration File:** `dhcp.conf`
*   **Address Range:** 10.37.129.15 - 10.37.129.20
*   **Default Lease Time:** 420
Then I started the isc-dhcp-server on the server
```bash
sudo system isc-dhcp-server start
```

Then went on the client and restarted the networking device so it connected to the server and checked the ip address to confirm
```bash
nmcli con down [id] && nmcli con up [id]
ip addr
```

### 5. SSH Hardening
Remote access was enabled via `openssh-server` making use of Ed25519 keys by configuring the sshs_config file. 
*   **Configuration File:** `interfaces.conf`
I started openssh-server on the server
```bash
sudo service ssh start
```

Then went on the client and generated the public and private keys for the secure connection, sent the public key to the server and then connected to the ssh
```bash
ssh-keygen -t ed25519
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@10.37.129.8
ssh user@10.37.129.8
```

## 📊 Verification & Screenshots
Below is proof of the functional network:

### DHCP Lease Success
![Client receiving IP](./screenshots/client-ip-check.png)
*Screenshot showing the client VM successfully binding to an IP from the DHCP pool.*

### SSH Connection
![SSH Terminal](./screenshots/ssh-connection.png)
*Screenshot showing a successful remote login from the client to the server.*

---
*This lab was completed as a demonstration of Linux system administration and networking fundamentals.*

