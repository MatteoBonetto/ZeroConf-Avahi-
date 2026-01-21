# ZeroConf Setup Guide for Linux and Windows

## Abstract

Using hostnames rather than IP addresses is easier and much more robust. In this guide, we learn how to set up a machine for advertising its address by using the **Avahi** (Linux) or **Bonjour** (Windows) protocol/service.

## Motivation

Using IP addresses for accessing other machines is bad practice, as the IP address of a given machine can change unexpectedly — for example, because it has been assigned by a DHCP server from a dynamic pool of available IPs.

Much more preferable is to use hostnames, that map to IP addresses dynamically. On the other hand, this requires a DNS server properly configured and updated.

**ZeroConf** comes to the rescue: it is an open protocol where all nodes on the same network advertise their services (ports) and host names by broadcasting, and by sharing the same, default local domain: `.local`.

> The name *ZeroConf* is a bit misleading, for actually there is a configuration step, although it’s minimal and simple. The configuration, additionally, is node-local, decentralized, and there is no need for any central server.

---

## How to Enable ZeroConf

### On macOS

ZeroConf is automatically available, so if your machine name is `MyMac`, your machine is available on the local network as `MyMac.local` (case-insensitive!).

### On Linux (with Avahi)

The same protocol and service go under the name of **Avahi**. To enable it, follow these steps:

---

### 1. Install the Needed Packages

```bash
sudo apt update && sudo apt install avahi-utils avahi-daemon openssh-server
```

---

### 2. Configure avahi name 

Open file named `/etc/avahi/avahi-daemon.conf ` with the following content:

Change in [server]:

host-name= test1

test1.local is the name visible on the local network

### 3. Apply the Changes

Enable and start:

```bash
sudo systemctl enable avahi-daemon
sudo systemctl start avahi-daemon
sudo systemctl enable ssh
sudo systemctl start ssh
```

---

### 4. Test It

Now you can use `<hostname>.local` in place of its IP address.

Try to ping test1.local

```bash
sudo systemctl restart avahi-daemon
sudo systemctl status avahi-daemon.service
```

To list all services advertised on the local network, use:

```bash
avahi-browse -a
```

### 4. Connect with SSH

```bash
ssh <pcusername>@<hostname>.local
```
Run this command from any other pc on the same local network

## Notes

- Make sure your firewall allows mDNS/Avahi traffic (UDP port 5353).
- Avahi supports both service publishing and browsing on the local network.


### On Windows (with Bonjour)

On Windows, the Avahi equivalent is **Bonjour**.

#### 1. Install Bonjour

Download and install:

[Bonjour Print Services for Windows](https://support.apple.com/en-us/106380)

#### 2. Check if Active

Press `WIN + R`, type `services.msc` and check that **Bonjour Service** is running.

#### 3. Change PC Name

Settings → System → Rename this PC → `<newpcname>` → Restart PC

Test with another pc to ping:

```bash
ping <newpcname>.local
```

#### 4. Set Network as Private and Disable Firewall

1. Connect to your local network  
2. Settings → Wi-Fi → Properties of `<network>` → Set as **Private**  
3. "Configure firewall and security settings" → Disable Firewall 
---

### SSH on Windows

Open **PowerShell as Administrator**:

#### 1. Configure

```bash
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
Start-Service sshd
Set-Service sshd -StartupType Automatic
```

#### 2. Check
Status:
```bash
Get-Service sshd
```

Current username:
```bash
whoami
```
`whoami` output: `<pcname>/<username>`

#### 2. Connect via SSH from another PC
ssh `<username>@<pcname>.local`


