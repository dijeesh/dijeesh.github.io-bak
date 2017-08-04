---
layout: post
title: Juniper SRX DHCP Configuration with Static binding
description: Juniper SRX DHCP Configuration with Static binding
Categories: [AWS]
tags: [networking, netops, juniper, srx, junos, junipernetworks, dijeeshpnair, devops, ]
comments: true
---

This article will guide you to configre your SRX firewall device as a DHCP server for your local networks and binding static IP Address for specific MAC Addresses.

<br>
| Example scenario |  |
| ------ | ------ |
| Private Zone subnet | 10.102.100.0/24|
| Private Zone interface | reth1.0 |
<br>

Steps:
---
<br>

**1 Configure DHCP Service**

Commit following rules to configure your SRX device to act as a DHCP Server.

```sh
set system services dhcp pool 10.102.100.0/24 address-range low 10.102.100.100
set system services dhcp pool 10.102.100.0/24 address-range high 10.102.100.200
set system services dhcp pool 10.102.100.0/24 default-lease-time 3600
set system services dhcp pool 10.102.100.0/24 domain-name lab-network.net
set system services dhcp pool 10.102.100.0/24 router 10.102.100.1
```

| Item | Description |
| ------ | ------ |
|address-range low | First IP address in your DHCP reserve pool |
|address-range high | Last IP address in your DHCP reserve pool |
|default-lease-time |  Local domain name |
| router | Gateway IP for your local network |

**2. Enable host-inbount-traffic for DHCP Service**

Now you’ve to enable dhcp under host-inbound-traffic rules for your SRX’s private network zone interface

```sh
set security zones security-zone PRIVATE_NETWORK interfaces reth1.0 host-inbound-traffic system-services dhcp
```

DHCP Service should be now running and it'll lease IP Address to the servers in your Local Network.

**3 Verify DHCP Bindings**

Run following command to verify DHCP bindings

```sh
show system services dhcp binding
```

**4 Static DHCP Bindings**

You can bind static IP Addresses to particluar server (Mac Address) as follows.

```sh
set system services dhcp static-binding 00:xx:xx:xx:xx:x fixed-address 10.102.100.xxx
```
