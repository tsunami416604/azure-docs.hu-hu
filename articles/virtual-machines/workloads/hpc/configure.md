---
title: Nagy teljesítményű számítástechnika – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg az Azure nagy teljesítményű számítástechnikai szolgáltatásait.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707827"
---
# <a name="optimization-for-linux"></a>Optimalizálás Linuxra

Ez a cikk néhány kulcsfontosságú technikát mutat be az operációs rendszer rendszerképének optimalizálásához. További információ a [InfiniBand engedélyezéséről](enable-infiniband.md) és az operációsrendszer-lemezképek optimalizálásáról.

## <a name="update-lis"></a>LIS frissítése

Ha egyéni rendszerkép használatával (például CentOS/RHEL 7,4 vagy 7,5) telepít üzembe helyezést, a virtuális gépen frissítse a LIS-t.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Memória visszaigénylése

A távoli memória-hozzáférés elkerülése érdekében a memória automatikus visszaigénylésével javítsa a hatékonyságot.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

A virtuális gépek újraindítása után tegye meg a következőket:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Tűzfal-és SELinux letiltása

Tiltsa le a tűzfal-és SELinux.

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

Cpupower letiltása.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>További lépések

* További információ a [InfiniBand engedélyezéséről](enable-infiniband.md) és az operációsrendszer-lemezképek optimalizálásáról.

* További információ az Azure-beli [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) -ről.
