---
title: Azure VMware-megoldások (AVS) – a számítási feladatok virtuális gépei migrálása az AVS Private Cloud szolgáltatásba
description: Ismerteti, hogyan telepíthet át virtuális gépeket helyszíni vCenter az AVS Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: af02bd947c73b670306704a10a09ca981b34c9a9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019995"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-avs-private-cloud-vcenter-environment"></a>A számítási feladatok virtuális gépei migrálása a helyszíni vCenter az AVS Private Cloud vCenter-környezetbe

A virtuális gépek helyszíni adatközpontból az AVS Private-felhőbe való áttelepítéséhez több lehetőség is rendelkezésre áll. Az AVS Private Cloud natív hozzáférést biztosít a VMware vCenter, a VMware által támogatott eszközöket pedig a munkaterhelések áttelepítéséhez használhatja. Ez a cikk a vCenter áttelepítési lehetőségeinek némelyikét ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A virtuális gépek és az adatok helyszíni adatközpontból történő áttelepítése megköveteli, hogy az adatközpont hálózati kapcsolata legyen az AVS Private Cloud Environment-környezettel. A hálózati kapcsolat létrehozásához használja a következő módszerek egyikét:

* [Helyek közötti VPN-kapcsolat](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni környezet és az AVS Private Cloud között.
* ExpressRoute Global Reach kapcsolatot a helyszíni ExpressRoute áramkör és egy AVS ExpressRoute áramkör között.

A helyszíni vCenter-környezet és az AVS Private Cloud közötti hálózati elérési útnak elérhetőnek kell lennie a virtuális gépek vMotion használatával történő áttelepítéséhez. A helyszíni vCenter lévő vMotion-hálózatnak útválasztási képességekkel kell rendelkeznie. Ellenőrizze, hogy a tűzfal engedélyezi-e az összes vMotion forgalmat a helyszíni vCenter és az AVS Private Cloud vCenter között. (Az AVS privát felhőben a vMotion-hálózat útválasztása alapértelmezés szerint konfigurálva van.)

## <a name="migrate-isos-and-templates"></a>ISO-és sablonok migrálása

Ha új virtuális gépeket szeretne létrehozni az AVS Private-felhőben, használja az ISO-ket és a virtuálisgép-sablonokat. Az ISO-és sablonok az AVS Private Cloud vCenter való feltöltéséhez és elérhetővé tételéhez használja a következő metódust.

1. Töltse fel az ISO-t az AVS Private Cloud vCenter az vCenter felhasználói felületéről.
2. [Hozzon közzé egy Content Library](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) -t az AVS Private Cloud vCenter:

    1. Tegye közzé a helyszíni tartalom-függvénytárat.
    2. Hozzon létre egy új tartalmat az AVS Private Cloud vCenter.
    3. Fizessen elő a közzétett helyszíni tartalom-függvénytárra.
    4. Szinkronizálja a tartalmi könyvtárat az előfizetett tartalmak eléréséhez.

## <a name="migrate-vms-using-powercli"></a>Virtuális gépek migrálása a PowerCLI használatával

Ha virtuális gépeket szeretne áttelepíteni a helyszíni vCenter az AVS Private Cloud vCenter, használja a VMware PowerCLI vagy a VMware Labs-ból elérhető Cross vCenter munkaterhelés áttelepítési segédprogramot. A következő minta parancsfájl a PowerCLI áttelepítési parancsokat mutatja be.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> A cél vCenter-kiszolgáló és ESXi-gazdagépek nevének használatához konfigurálja a DNS-továbbítást a helyszínről az AVS Private Cloud-ra.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Virtuális gépek migrálása a NSX 2. rétegbeli VPN használatával

Ez a beállítás lehetővé teszi a számítási feladatok élő áttelepítését a helyszíni VMware-környezetből az Azure-beli AVS Private-felhőbe. Ezzel a kibővített 2. rétegbeli hálózattal a helyszíni alhálózat elérhető lesz az AVS Private Cloud-on. Az áttelepítés után az új IP-cím hozzárendelése nem szükséges a virtuális gépekhez.

A számítási [feladatok migrálása a 2. rétegbeli hálózatokkal című szakasz](migration-layer-2-vpn.md) leírja, hogyan használható a 2. RÉTEGbeli VPN a 2. rétegbeli hálózatnak a helyszíni környezetből az AVS Private Cloud-ba való kiépítéséhez.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Virtuális gépek migrálása biztonsági mentési és vész-helyreállítási eszközök használatával

A virtuális gépeknek az AVS Private Cloud-ba történő áttelepítése a biztonsági mentési/visszaállítási eszközök és a vész-helyreállítási eszközök használatával végezhető el. A külső féltől származó eszközzel létrehozott biztonsági másolatokból történő visszaállításhoz használja az AVS Private Cloud szolgáltatást. Az AVS Private Cloud a VMware SRM vagy egy harmadik féltől származó eszköz használatával vész-helyreállítási célpontként is használható.

Ezen eszközök használatával kapcsolatos további információkért tekintse meg a következő témaköröket:

* [A számítási feladatok virtuális gépei biztonsági mentése az AVS Private Cloud-on a Veeam B & R használatával](backup-workloads-veeam.md)
* [Az AVS Private Cloud beállítása vész-helyreállítási webhelyként a helyszíni VMware-alapú számítási feladatokhoz](disaster-recovery-zerto.md)
