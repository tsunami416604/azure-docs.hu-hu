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
ms.openlocfilehash: 0d890553ee145ca6aafed5a34d158c6a34d9af36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358168"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Azure VMware megoldás által CloudSimple felügyeleti alapfogalmak

Administering Azure VMware megoldás által CloudSimple szükséges a következő fogalmak ismertetése.

* CloudSimple szolgáltatás (jelenik meg az Azure VMware megoldás CloudSimple - szolgáltatás)
* CloudSimple csomópont (jelenik meg az Azure VMware megoldás CloudSimple - csomópont)
* CloudSimple Private Cloud
* Hálózati szolgáltatás
* CloudSimple virtuális gép (megjelenő Azure VMware megoldás CloudSimple – virtuális gép által)

## <a name="cloudsimple-service"></a>CloudSimple service

A CloudSimple szolgáltatás létrehozása és kezelése az Azure Portalról CloudSimple által a VMware-megoldásokkal kapcsolódó összes erőforrás teszi lehetővé. Service erőforrás létrehozása minden olyan régióban, ahol a szolgáltatás használni kívánja. 

Tudjon meg többet a [CloudSimple szolgáltatás](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>CloudSimple csomópont

CloudSimple csomópontnak számít egy dedikált, operációs rendszer nélküli számítógépen, a hiperkonvergens számítási és tárolási gazdagépet, amelybe a VMware ESXi-hipervizorra telepítve van. Ez a csomópont majd építeni a VMware vSphere, a vCenter, a vsan-hoz és a NSX platformok. CloudSimple hálózati szolgáltatásokat és a hálózati biztonsági szolgáltatások is engedélyezve van. Minden egyes csomópont szolgálja ki olyan adategység, számítási és tárolási kapacitás létrehozása vásárolható meg [CloudSimple Magánfelhők](cloudsimple-private-cloud.md). Vásároljon, vagy foglaljon le a csomópontok egy régióban, ahol a CloudSimple szolgáltatás érhető el.


Tudjon meg többet [CloudSimple csomópontok](cloudsimple-node.md)

## <a name="cloudsimple-private-cloud"></a>CloudSimple Private Cloud

CloudSimple Magánfelhő egy elkülönített VMware stack környezet felügyeli a vCenter-kiszolgáló a saját felügyeleti tartományban. VMware-környezetet ESXi gazdagépek, a vSphere, vCenter, vsan-hoz és NSX tartalmazza.  A stack fut, a dedikált csomópontok (elkülönített és dedikált az operációs rendszer nélküli hardver), és a felhasználók keresztül, amely tartalmazza a vCenter- és NSX Manager natív VMware-eszközök által felhasznált. Dedikált csomópontok Azure-helyen vannak üzembe helyezve, és az Azure által felügyelt. Minden egyes Magánfelhő szegmentált, és védi hálózati szolgáltatások, például a VLAN-OK/alhálózatok és a tűzfal-táblákat.  Biztonságos, személyes VPN használatával csatlakozhat a helyszíni környezet és az Azure-hálózat jön létre, és az Azure ExpressRoute-kapcsolatok.

Tudjon meg többet [CloudSimple privát felhő](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Hálózati szolgáltatás

A CloudSimple szolgáltatás segítségével egy hálózati régiónként a CloudSimple szolgáltatás telepítési helyét. A hálózat egy egyetlen 3. rétegbeli TCP-címterületek IP-útválasztás alapértelmezés szerint engedélyezve. Az összes Magánfelhők és alhálózatok ebben a régióban létrehozott további konfiguráció nélkül kommunikálhatnak egymással. Elosztott portcsoportokat a vcenter a VLAN-ok használatával hoz létre.  A következő hálózati szolgáltatások használatával konfigurálhatja, és a számítási erőforrások védelmét a privát felhőben.

* [VLAN-OK/alhálózatok](cloudsimple-vlans-subnets.md)
* [Tűzfal-táblák](cloudsimple-firewall-tables.md)
* [VPN-átjárók](cloudsimple-vpn-gateways.md)
* [Nyilvános IP-cím](cloudsimple-public-ip-address.md)
* [Az Azure hálózati kapcsolat](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Virtuális gép CloudSimple

A CloudSimple szolgáltatás lehetővé teszi, hogy a VMware virtuális gépek kezelése az Azure Portalról. Egy vagy több fürtöket, vagy a vSphere-környezetből az erőforráskészletek is le lehet képezni az előfizetést, amelyen a szolgáltatás létrehozása.

További információk:

* [CloudSimple virtuális gépek](cloudsimple-virtual-machines.md)
* [Azure Subscription Mapping](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
