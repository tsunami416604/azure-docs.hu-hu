---
title: Azure VMware-megoldás a CloudSimple-től – A számítási feladatok virtuális gépeiáttelepítése a privát felhőbe
description: A virtuális gépek áttelepítése a helyszíni vCenterből a CloudSimple Private Cloud vCenter be
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019995"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Számítási virtuális gépek áttelepítése a helyszíni vCenterből a Private Cloud vCenter-környezetbe

Virtuális gépek áttelepítése egy helyszíni adatközpontból a CloudSimple private cloud, számos lehetőség áll rendelkezésre.  A magánfelhő natív hozzáférést biztosít a VMware vCenterhez, és a VMware által támogatott eszközök a számítási feladatok áttelepítéséhez használhatók. Ez a cikk a vCenter áttelepítési lehetőségeinek egy részét ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Virtuális gépek és a helyszíni adatközpontból származó adatok áttelepítése hálózati kapcsolatot igényel az adatközpontból a privát felhőkörnyezetbe.  A hálózati kapcsolat létrehozásához használja az alábbi módszerek egyikét:

* Helyek közötti [VPN-kapcsolat](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni környezet és a magánfelhő között.
* ExpressRoute globális elérési kapcsolat a helyszíni ExpressRoute-kapcsolat és a CloudSimple ExpressRoute-kapcsolat között.

A hálózati elérési út a helyszíni vCenter-környezetből a privát felhőbe elérhetőnek kell lennie a vMotion használatával a virtuális gépek áttelepítéséhez.  A helyszíni vCenter vMotion-hálózatának útválasztási képességekkel kell rendelkeznie.  Ellenőrizze, hogy a tűzfal engedélyezi-e a helyszíni vCenter és a Private Cloud vCenter közötti összes vMotion-forgalmat. (A magánfelhőben a vMotion hálózaton való útválasztás alapértelmezés szerint be van állítva.)

## <a name="migrate-isos-and-templates"></a>Az ISO-k és sablonok áttelepítése

Ha új virtuális gépeket szeretne létrehozni a magánfelhőben, használja az ISO-k és a virtuálisgép-sablonok használatát.  Az ISO-k és sablonok feltöltéséhez és a private cloud vCenter szolgáltatásba való elérhetővé tenni, használja a következő módszert.

1. Töltse fel az ISO-t a private cloud vCenter-be a vCenter felhasználói felületéről.
2. [Tartalomtár közzététele](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) a Private Cloud vCenter szolgáltatásban:

    1. Tegye közzé a helyszíni tartalomtárat.
    2. Hozzon létre egy új tartalomtárat a Private Cloud vCenteren.
    3. Iratkozzon fel a közzétett helyszíni tartalomtárra.
    4. Szinkronizálja a tartalomtárat az előfizetett tartalmakhoz való hozzáféréshez.

## <a name="migrate-vms-using-powercli"></a>Virtuális gépek áttelepítése a PowerCLI használatával

Virtuális gépek áttelepítése a helyszíni vCenter a private cloud vCenter, használja vMware PowerCLI vagy a Cross vCenter számítási feladatok áttelepítése segédprogram érhető el vmware Labs.  A következő mintaparancsfájl a PowerCLI áttelepítési parancsait mutatja be.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> A cél vCenter-kiszolgáló és az ESXi-állomások nevének használatához konfigurálja a DNS-továbbítást a helyszíni webfelhőbe.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Virtuális gépek áttelepítése NSX Layer 2 VPN használatával

Ez a beállítás lehetővé teszi a számítási feladatok élő áttelepítését a helyszíni VMware környezetből az Azure-beli magánfelhőbe.  Ezzel a kiterjesztett Layer 2 hálózattal a helyszíni alhálózat elérhető lesz a magánfelhőben.  Áttelepítés után a virtuális gépekhez nem szükséges új IP-címhozzárendelés.

[A 2.](migration-layer-2-vpn.md)

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Virtuális gépek áttelepítése biztonsági mentési és vész-helyreállítási eszközökkel

Virtuális gépek áttelepítése a privát felhőbe biztonsági mentési/visszaállítási eszközök és vész-helyreállítási eszközök használatával végezhető el.  Használja a magánfelhőt célként a külső gyártótól származó eszközzel létrehozott biztonsági mentések visszaállításához.  A magánfelhő a VMware SRM vagy egy külső gyártó tól származó eszköz használatával is használható vész-helyreállítási célként.

Az eszközök használatával további információt az alábbi témakörökben talál:

* [A Számítási feladatok virtuális gépeinek biztonsági és biztonsági és biztonsági, a(z) Veeam B&R használatával](backup-workloads-veeam.md)
* [A CloudSimple Private Cloud beállítása vész-helyreállítási helyként a helyszíni VMware-számítási feladatokhoz](disaster-recovery-zerto.md)
