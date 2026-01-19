# ZeroConf Setup Guide for Linux

## Abstract

Using hostnames rather than IP addresses is easier and much more robust. In this guide, we learn how to set up a Linux box for advertising its address by using the **Avahi** protocol/service.

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
sudo systemctl status avahi-daemon.service
```

### 4. Connect with SSH

```bash
ssh <pcusername>@<hostname>.local
```
Run this command from any other pc on the same local network

## Notes

- Make sure your firewall allows mDNS/Avahi traffic (UDP port 5353).
- Avahi supports both service publishing and browsing on the local network.
