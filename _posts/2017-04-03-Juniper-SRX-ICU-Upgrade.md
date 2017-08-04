---
layout: post
title: Juniper SRX Cluster – Branch Devices – ICU Upgrade
description: Juniper SRX Cluster – Branch Devices – ICU Upgrade
#image: /assets/media/Lamp.jpg
Categories: [Networking]
tags: [networking, netops, juniper, srx, junos, junipernetworks, dijeeshpnair, devops, ]
comments: true
---


JunOS versions on Branch devices (SRX100, SRX210, SRX220, SRX240, SRX550, and SRX650 Services Gateways) in a chassis cluster can be upgraded using  in-band cluster upgrade (ICU) method. Downtime required for this type of upgrade method is less than 30 seconds.


Notes
----
<br>

* Drop in traffic (`30 seconds approximately`)

* Loss of security flow sessions. ICU is available with the no-sync option only. Ie; flow states will not be synced to secondary node when one node is rebooting while upgrading.

* ICU method is available only on Junos OS releases 11.2R2 and later

* ICU method cannot be used to downgrade to a build earlier than Junos OS 11.2R2

* Make sure sufficient disk spaces are available on both nodes.


<br>

Upgrade Steps
===
<br>

1 Confirm Junos OS Version running on the devices
```sh
$ show version
```
2 Confirm serial console access to the devices

3 Perform storage clean-up
```sh
$ request system storage cleanup
```
4 Upload latest Junos OS to `/var/tmp` of node0

5 Perform ICU upgrade
```sh
$ request system software in-service-upgrade <image_name> no-sync
```
6 Monitor the upgrade process in console session


<br>
Upgrade Procedure + Console logs
----
<br>

**Validate package and copy to secondary node**

```sh
ISSU: Validating package WARNING: in-service-upgrade shall reboot both the nodes in your cluster. Please ignore any subsequent reboot request message ISSU: start downloading software package on secondary node Pushing bundle to node1
```

**Create alternate root partition and extract the image to alternate root partition**

```sh
Formatting alternate root (/dev/da0s1a)… /dev/da0s1a: 296.9MB (607996 sectors) block size 16384, fragment size 2048 Extracting /var/tmp/junos-srxsme-12.1X46-D55.3-domestic.tgz …
```

**ISSU upgrades secondary and then primary node**

```sh
JUNOS 12.1X46-D55.3 will become active at next reboot ISSU: finished upgrading on secondary node node1 ISSU: start upgrading software package on primary node JUNOS 12.1X46-D55.3 will become active at next reboot
```

**Fail over all redundancy groups to node0**
```sh
ISSU: failover all redundancy-groups 1…n to primary node Successfully reset all redundancy-groups priority back to configured priority. node1: ————————————————————————– Successfully reset all redundancy-groups priority back to configured priority. node0:  ————————————————————————– Initiated manual failover for all redundancy-groups to node0
```

**Reboot secondary node**
```sh
ISSU: rebooting Secondary Node
```

**Reboot primary node, when secondary node is back online**
```sh
ISSU: Waiting for secondary node node1 to reboot. ISSU: Waiting for node 1 to come up ISSU: node 1 came up ISSU: secondary node node1 booted up.
```


<br>
Aborting ICU upgrade
-----
<br>

At any time during the upgrade process, you can abort by running

```sh
request system software abort in-service-upgrade
```

If you have given the abort command during / after secondary node reboots. Your cluster will be un an inconsistent state. ie; secondary node will be running with new version than primary node. You should rollback the upgrade on secondary node by running following commands.

<br>
**Abort upgrade**
```sh
request system software abort in-service-upgrade
```

**Roll back to previous node**
```sh
request system software rollback node < node-id >
```

**Reboot node**
```sh
request system reboot
```

<br>
**Ref:**
- Juniper KB Articles [SRX] ISSU/ICU upgrade limitations on SRX firewalls
- Upgrading Devices in a Chassis Cluster Using ICU

<br>

Happy Upgrade :)
