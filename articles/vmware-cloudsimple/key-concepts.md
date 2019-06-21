---
title: Azure VMware megoldás által CloudSimple felügyeletével kapcsolatos alapfogalmak
description: Azure VMware megoldás által CloudSimple felügyeletével kapcsolatos alapfogalmakat ismerteti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3eff61408cb190396987ace6dee21182cff4f25c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165188"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Azure VMware megoldás által CloudSimple felügyeleti alapfogalmak

Azure VMware megoldás által CloudSimple felügyelete megköveteli a következő fogalmak megismerése:

* CloudSimple szolgáltatás, amely szerint CloudSimple - szolgáltatás Azure VMware megoldás jelenik meg
* CloudSimple csomópont, Azure VMware megoldás által CloudSimple - csomópont jelenik meg
* CloudSimple privát felhő
* Hálózati szolgáltatás
* CloudSimple virtuális gépet, mely szerint CloudSimple – virtuális gép Azure VMware megoldás jelenik meg

## <a name="cloudsimple-service"></a>CloudSimple service

A CloudSimple szolgáltatás is létrehozása és kezelése az Azure Portalról CloudSimple által a VMware-megoldásokkal kapcsolódó összes erőforrás. Service erőforrás létrehozása minden olyan régióban, ahol a szolgáltatás használni kívánja.

Tudjon meg többet a [CloudSimple szolgáltatás](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple csomópont

CloudSimple csomópontnak számít egy hiperkonvergens dedikált, operációs rendszer nélküli, számítási és tárolási gazdagépet, amelybe a VMware ESXi-hipervizorra telepítve van. Ez a csomópont majd építeni a VMware vSphere, a vCenter, a vsan-hoz és a NSX platformok. CloudSimple hálózati szolgáltatásokat és a hálózati biztonsági szolgáltatások is engedélyezve van. Minden egyes csomópont szolgálja ki olyan adategység, hozhat létre helyezheti üzembe a számítási és tárolási kapacitást [CloudSimple magánfelhők](cloudsimple-private-cloud.md). Üzembe helyezése, vagy foglaljon le a csomópontok egy régióban, ahol a CloudSimple szolgáltatás érhető el.


Tudjon meg többet [CloudSimple csomópontok](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple privát felhő

CloudSimple magánfelhő egy elkülönített VMware stack környezet felügyeli a vCenter-kiszolgáló a saját felügyeleti tartományban. A VMware-környezetet ESXi gazdagépek, a vSphere, vCenter, vsan-hoz és NSX tartalmazza. A stack fut, a dedikált csomópontok (elkülönített és dedikált az operációs rendszer nélküli hardver), és a felhasználók keresztül, amely tartalmazza a vCenter- és NSX Manager natív VMware-eszközök által felhasznált. Dedikált csomópontok Azure-helyen vannak üzembe helyezve, és az Azure által felügyelt. Minden egyes magánfelhő is szegmentált és hálózati szolgáltatásokat, például a virtuális helyi hálózatok és alhálózatok és tűzfal táblák használatával biztonságossá. Csatlakozhat a helyszíni környezet és az Azure-hálózatot hoznak létre használatával biztonságos, személyes VPN- és Azure ExpressRoute kapcsolatok.

Tudjon meg többet [CloudSimple magánfelhő](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Hálózati szolgáltatás

A CloudSimple szolgáltatás segítségével egy hálózati régiónként a CloudSimple szolgáltatás telepítési helyét. A hálózat egy egyetlen 3. rétegbeli TCP-címterületek IP-útválasztás alapértelmezés szerint engedélyezve. Az összes magánfelhők és alhálózatok ebben a régióban létrehozott további konfiguráció nélkül kommunikálhatnak egymással. Elosztott portcsoportokat a vCenter-, a VLAN-ok használatával létrehozott. A következő hálózati szolgáltatások használatával konfigurálhatja, és a magánfelhő a számítási erőforrások védelmét:

* [Virtuális helyi hálózatok és alhálózatok](cloudsimple-vlans-subnets.md)
* [Tűzfal-táblák](cloudsimple-firewall-tables.md)
* [VPN-átjárók](cloudsimple-vpn-gateways.md)
* [Nyilvános IP-cím](cloudsimple-public-ip-address.md)
* [Az Azure hálózati kapcsolat](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Virtuális gép CloudSimple

A CloudSimple szolgáltatással a VMware virtuális gépek az Azure Portalról kezelheti. Egy vagy több fürtöket, vagy a vSphere-környezetből az erőforráskészletek is le lehet képezni az előfizetést, amelyen a szolgáltatás létrehozása.

További információk:

* [CloudSimple virtuális gépek](cloudsimple-virtual-machines.md)
* [Azure-előfizetés leképezés](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
