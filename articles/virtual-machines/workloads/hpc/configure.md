---
title: Nagy teljesítményű számítástechnika - Azure virtuális gépek | Microsoft dokumentumok
description: Ismerje meg a nagy teljesítményű számítástechnikát az Azure-ban.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707827"
---
# <a name="optimization-for-linux"></a>Optimalizálás Linuxra

Ez a cikk néhány kulcsfontosságú technikát mutat be az operációs rendszer lemezképének optimalizálásához. További információ az [InfiniBand engedélyezéséről](enable-infiniband.md) és az operációs rendszer lemezképeinek optimalizálásáról.

## <a name="update-lis"></a>LIS frissítése

Ha egyéni lemezkép használatával (például egy régebbi operációs rendszer, például a CentOS/RHEL 7.4 vagy 7.5) használatával telepíti a rendszert, frissítse a LIS-t a virtuális gépen.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Memória visszaszerzése

Növelje a hatékonyságot a memória automatikus visszanyerésével, hogy elkerülje a távoli memória-hozzáférést.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

A virtuális gép újraindítása után is megmarad:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Tűzfal és SELinux letiltása

Tiltsa le a tűzfalat és az SELinux-ot.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Cpupower letiltása

Tiltsa le a cpupower-t.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>További lépések

* További információ az [InfiniBand engedélyezéséről](enable-infiniband.md) és az operációs rendszer lemezképeinek optimalizálásáról.

* További információ az Azure-beli [HPC-ről.](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)
