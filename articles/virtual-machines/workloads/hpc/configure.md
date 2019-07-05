---
title: Nagy teljesítményű feldolgozás – az Azure Virtual Machines |} A Microsoft Docs
description: Tudnivalók a nagy teljesítményű feldolgozás, az Azure-ban.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: e8ff4147130dfeff14be41ed292b51ed34966df0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537679"
---
# <a name="optimization-for-linux"></a>Optimalizálás Linuxra

Ez a cikk bemutatja néhány kulcsfontosságú technikák optimalizálhatja az operációs rendszer lemezképe. Tudjon meg többet [InfiniBand engedélyezése](enable-infiniband.md) és optimalizálása az operációsrendszer-lemezképeket.

## <a name="update-lis"></a>LIS frissítése

Üzembe (például egy régebbi operációs rendszer például a CentOS vagy RHEL 7.4 vagy 7.5) egyéni rendszerkép használatával, frissítse a virtuális gép LIS.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Memória felszabadítása

Hatékonyságnövelés által automatikusan igényelni a memória a távoli memória-hozzáférés elkerülése érdekében.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Ez a virtuális gép újraindítása után is fennállnak legyen:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Tűzfal- és SELinux letiltása

Tiltsa le tűzfal- és SELinux.

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

Tiltsa le a cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [InfiniBand engedélyezése](enable-infiniband.md) és optimalizálása operációsrendszer-lemezképeket.

* Tudjon meg többet [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
