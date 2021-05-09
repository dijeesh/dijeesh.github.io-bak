---
layout: post
title: Applying XenServer Patches
description: Applying XenServer Patches
#image: /assets/media/Lamp.jpg
Categories: [Virtualization]
tags: [networking, virtualization, xenserver, xen, upgrade, patching, sysops, dijeeshpnair, devops]
comments: true
---

For better performance and security it’s recommended to install all patches released by XenServer. In this article I would like to share the steps for applying patches on XenServer and XenServer pool

<br>
Ref following Citrix Article to get list of recommended patches for your XenServer Version.

```sh
https://support.citrix.com/article/CTX138115`
```

 <br>

Single XenServer Host
---

 <br>
 If you’re running XenServer on a single hardware node, following steps can be used to install patches.

<br>
**1. Download Hotfix from Citrix Downloads Portal**

**2. Unzip Hotfix**

```sh
unzip patchxxx.zip
```

**3. Get Host UUID**

```sh
xe host-list
```

**4. Upload hotfix**

```sh
xe patch-upload file-name=<patchxxx.xsupdate>   
```

**5. Apply patch**

```sh
xe patch-apply uuid=<patch UUID step 4>  host-uuid=<host UUID step 3>
```
<br>
<br>

XenServer pool
---

<br>
If you’re running a XenServer Pool with shared storage, maintenance for a hardware node will not affect the running VMs. You can issue xenmotion to migrate VMs from one server to another when one servers is going to reboot.

In this case, you should download patches to your pool master and apply patches to the entire pool using `patch-pool-apply` command. Then evacuate and reboot hosts starting from pool master.

<br>

Steps for Installing patches in XenServer pool are as follows.

<br>

**1. Take pool database backup**
```sh
xe pool-dump-database file-name=pool-database-backup_`$date +%F`
```
**2. Disable pool HA**
```sh
xe pool-ha-disable
```
**3. Eject all CD Drives (mounted to the VMs)**
```sh
xe vm-cd-eject –multiple
```
**4. Download hotfixes to pool master /root/hotfix/**

**5. Apply Hotfixes**

**6. Perform post install actions**

Reboot Hosts / restart tool-stack based on the guidelines provided while applying patch.

<br>

Applying patches
---

You can either use `xe patch-pool-apply` from the Pool master or use this [script](https://gist.github.com/dijeesh/2ca76f6cf25110c97a88a281b060b6b6) to upload and apply patches.

```sh
xe patch-pool-apply uuid=<patch UUID >  
```

<br>

**Script usage:-**

<br>

* Download the script and save  `/root/hotfix/apply-hotfix.sh`
* Set permissions for the script `chmod +x /root/hotfix/apply-hotfix.sh`
* Apply Patches `./apply-hotfix.sh <patch.xsupdate>`

<br>
This will upload and apply patches on all pool members. Once finished, you may evacuate and reboot pool members starting from the pool master or restart tool-stack as instructed while applying the patch.

Happy Upgrade 🙂
