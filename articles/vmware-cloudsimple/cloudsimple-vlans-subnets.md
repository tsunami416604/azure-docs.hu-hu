---
title: VLAN-ok és alhálózatok az Azure VMware-megoldásában a CloudSimple-től
description: Tudjon meg többet a VLAN-okról és alhálózatokról a CloudSimple private cloud szolgáltatásban
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024891"
---
# <a name="vlans-and-subnets-overview"></a>VLAN-ok és alhálózatok – áttekintés

A CloudSimple régiónként biztosít egy hálózatot, ahol a CloudSimple szolgáltatás telepítve van.  A hálózat egyetlen TCP Layer 3 címterület, ahol az útválasztás alapértelmezés szerint engedélyezve van.  Az ebben a régióban létrehozott összes privát felhő és alhálózat további konfiguráció nélkül kommunikálhat egymással.  A VLAN-ok használatával elosztott portcsoportokat hozhat létre a vCenterben.

![CloudSimple hálózati topológia](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Vlan

Minden magánfelhőhöz létrejön egy VLAN (Layer 2 hálózat).  A Layer 2-forgalom egy privát felhő határain belül marad, így elkülönítheti a helyi forgalmat a magánfelhőn belül.  A magánfelhőben létrehozott VLAN csak az adott magánfelhőben hozhat létre elosztott portcsoportokat.  A magánfelhőn létrehozott VLAN automatikusan konfigurálódik a magánfelhő állomásaihoz csatlakoztatott összes kapcsolón.

## <a name="subnets"></a>Alhálózatok

Alhálózatot az alhálózat címterének meghatározásával hozhat létre, amikor VLAN-t hoz létre. A címterületről származó IP-cím alhálózati átjáróként van hozzárendelve. Ügyfélenként és régiónként egyetlen privát 3. Az 1918-as számú RFC-szolgáltatás nem átfedő címterét konfigurálhatja a helyszíni hálózattal vagy az Azure virtuális hálózatával a hálózati régióban.

Az összes alhálózat alapértelmezés szerint kommunikálhat egymással, csökkentve a magánfelhők közötti útválasztás konfigurációs terhelését. Az azonos régióban lévő számítógépek kelet-nyugati adatai ugyanabban a Layer 3-as hálózatban maradnak, és a régión belüli helyi hálózati infrastruktúrán keresztül továbbítódnak. Nincs szükség kimenő forgalomra a magánfelhők közötti kommunikációhoz egy régióban. Ez a megközelítés kiküszöböli a WAN/egress teljesítménybüntetést a különböző számítási feladatok különböző privát felhőkben történő üzembe helyezésekor.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN alhálózatok CIDR tartomány

A magánfelhő egy vCenter-kiszolgáló által felügyelt elkülönített VMware-veremként (ESXi-gazdagépek, vCenter, vSAN és NSX) jön létre.  A felügyeleti összetevők a CIDR vSphere/vSAN alhálózatokhoz kiválasztott hálózatban vannak telepítve.  A hálózati CIDR-tartomány a központi telepítés során különböző alhálózatokra van felosztva.

* Minimális vSphere/vSAN alhálózatok CIDR tartományelőtagja: **/24**
* Maximális vSphere/vSAN alhálózatok CIDR tartományelőtagja: **/21**

> [!CAUTION]
> A vSphere/vSAN CIDR tartományban lévő IP-címek a private cloud infrastruktúra számára vannak fenntartva.  Ne használja az IP-címet ebben a tartományban semmilyen virtuális gépen.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN alhálózatok CIDR tartománykorlátai

A vSphere/vSAN alhálózatok CIDR tartományméretének kiválasztása hatással van a privát felhő méretére.  Az alábbi táblázat a CIDR vSphere/vSAN alhálózatok mérete alapján használható csomópontok maximális számát mutatja be.

| Meghatározott vSphere/vSAN alhálózatok CIDR előtag hossza | Csomópontok maximális száma |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Privát felhőben létrehozott felügyeleti alhálózatok

A következő felügyeleti alhálózatok jönnek létre, amikor létrehoz egy privát felhő.

* **Rendszerkezelés**. VLAN és alhálózat az ESXi állomások felügyeleti hálózatához, DNS-kiszolgálóhoz, vCenter-kiszolgálóhoz.
* **VMotion**. VLAN és alhálózat az ESXi állomások vMotion hálózatához.
* **VSAN**. VLAN és alhálózat az ESXi állomások vSAN-hálózatához.
* **NsxtEdgeUplink1**. VLAN és alhálózat VLAN uplinks egy külső hálózatra.
* **NsxtEdgeUplink2**. VLAN és alhálózat VLAN uplinks egy külső hálózatra.
* **NsxtEdgeTransport**. A VLAN és a szállítási zónák alhálózata szabályozza a Layer 2 hálózatok elérését az NSX-T-ben.
* **NsxtHostTransport**. VLAN és alhálózat a gazdaszállítási zónához.

### <a name="management-network-cidr-range-breakdown"></a>Felügyeleti hálózati CIDR tartomány lebontása

VSphere/vSAN alhálózatok CIDR tartományban megadott van osztva több alhálózat.  Az alábbi táblázat az engedélyezett előtagok bontásának példáját mutatja be.  A példa a 192.168.0.0 cidr tartományt használja.

Példa:

| Meghatározott vSphere/vSAN alhálózatok CIDR/előtag | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Rendszerkezelés | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN között | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T állomás átvitele | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T élátvitel | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T él-uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T él-uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>További lépések

* [Virtuális helyi hálózatok és alhálózatok létrehozása és kezelése](create-vlan-subnet.md)
