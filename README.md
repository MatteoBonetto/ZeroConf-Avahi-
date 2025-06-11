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

### 2. Configure the Service

Create a file named `/etc/avahi/services/ssh.service` with the following content:

```xml
<!-- See avahi.service(5) for more information about this configuration file -->
<service-group>
  <name replace-wildcards="yes">%h</name>
  <service>
    <type>_ssh._tcp</type>
    <port>22</port>
  </service>
</service-group>
```

This configuration makes the host accessible as `%h.local`, where `%h` is a wildcard for the current hostname, and also advertises the SSH service on TCP port 22.

To list all services advertised on the local network, use:

```bash
avahi-browse -a
```

---

### 3. Apply the Changes

Enable and start the service:

```bash
sudo systemctl enable avahi-daemon
sudo systemctl start avahi-daemon
```

---

### 4. Test It

Now you can use `<hostname>.local` in place of its IP address.

For example, when launching **MADS agents**, assuming the broker is running on a device with hostname set to `mads-broker`, you can simply use:

```bash
mads source -s tcp://mads-broker.local:9092 my_plugin.plugin
```

---

## Notes

- Make sure your firewall allows mDNS/Avahi traffic (UDP port 5353).
- Avahi supports both service publishing and browsing on the local network.
