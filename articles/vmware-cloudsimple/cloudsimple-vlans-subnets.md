---
title: Virtuális helyi hálózatok és alhálózatok a VMware megoldás által CloudSimple – Azure
description: További információ a virtuális helyi hálózatok és alhálózatok CloudSimple magánfelhőben
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 448b2b51ef6c5f7aa0bd660eec580019714caf5d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577930"
---
# <a name="vlans-and-subnets-overview"></a>Virtuális helyi hálózatok és alhálózatok áttekintése

CloudSimple biztosít egy hálózati régiónként a CloudSimple szolgáltatás telepítési helyét.  A hálózat egy egyetlen 3. rétegbeli TCP-címterületek IP-útválasztás alapértelmezés szerint engedélyezve.  Az összes magánfelhők és alhálózatok ebben a régióban létrehozott kommunikálhatnak egymással további konfiguráció nélkül.  Elosztott portcsoportokat a vcenter-kiszolgáló a VLAN-ok használatával is létrehozhat.

## <a name="vlans"></a>VLAN-OK

VLAN-ok (2. rétegbeli hálózati) privát felhő / jönnek létre.  A 2. rétegbeli forgalom a magánfelhő, amely lehetővé teszi, hogy elkülönítse a helyi adatforgalmat a magánfelhőhöz belül marad.  Egy VLAN-t a magánfelhőben létrehozott elosztott port csoportok létrehozása csak a privát felhőben használható.  Egy VLAN-hoz létre a magánfelhő konfigurálása automatikusan történik meg a magánfelhő a gazdagépekhez csatlakozó összes kapcsolók.

## <a name="subnets"></a>Alhálózatok

Egy alhálózat is létrehozhat, ha egy VLAN-t, az alhálózat címtartománya a definiálásával jön létre. A címtér az IP-cím hozzá van rendelve az átjáró alhálózatot. Egyetlen titkos 3. rétegbeli címtér ügyfél és a régió van hozzárendelve. A helyszíni hálózat vagy az Azure virtual network bármely RFC 1918 egymást nem átfedő címterek konfigurálhatja a hálózati régióban.

Összes alhálózat-alapértelmezés szerint a konfigurációját többletterhelést okoz a privát felhők közötti útválasztást csökkentése kommunikálhatnak egymással. Kelet – Nyugat-adatokkal ugyanabban a régióban a számítógépek egyaránt ugyanazon a 3. rétegbeli hálózaton marad, és átviszi a helyi hálózati infrastruktúrán a régión belül. Nincs kimenő forgalom nem egy régióban privát felhők közötti kommunikációhoz szükséges. Ez a megközelítés kiküszöböli a különböző magánfelhőkben különböző számítási feladatok üzembe helyezése bármely WAN/kimenő forgalom teljesítményét.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN-alhálózatokat CIDR-tartományt

Magánfelhő létrehozása egy elkülönített VMware-környezetet (ESXi-gazdagépek, vCenter, vsan-hoz és NSX), a vCenter-kiszolgáló által felügyelt környezetben.  A kiválasztott hálózati összetevőket vannak üzembe helyezve **vSphere/vSAN-alhálózatokat CIDR**.  A hálózati CIDR-tartományt az üzembe helyezés során különböző alhálózatokon van felosztva.

Minimális vSphere/vSAN-alhálózatokat CIDR-tartományt előtag: **/24** maximális vSphere/vSAN-alhálózatokat CIDR-tartományt előtag:   **/21-én**

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN-alhálózatokat CIDR-tartományt korlátok

A Magánfelhő méretének vSphere/vSAN-alhálózatokat CIDR tartomány méretének hatással van.  Az alábbi táblázat az Ön is rendelkezik a mérete alapján vSphere/vSAN-alhálózatokat CIDR csomópontok maximális számát.

| Megadott vSphere/vSAN-alhálózatokat CIDR-előtag hossza | Csomópontok maximális száma |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>A Magánfelhő létrehozott felügyeleti alhálózatok

Magánfelhő létrehozásakor jönnek létre következő felügyeleti alhálózatokra. 

* **Rendszer-felügyeleti** -VLAN-alhálózat az ESXi-gazdagépek felügyeleti hálózati, DNS-kiszolgáló, a vCenter-kiszolgálót.
* **VMotion** -VLAN és az ESXi-gazdagépek vMotion hálózati alhálózatot.
* **VSAN** -VLAN és az ESXi-gazdagépek vsan-hoz hálózati alhálózatot.
* **NsxtEdgeUplink1** -VLAN és a VLAN kimenő portokhoz külső hálózat, alhálózat.
* **NsxtEdgeUplink2** -VLAN és a VLAN kimenő portokhoz külső hálózat, alhálózat.
* **NsxtEdgeTransport** -VLAN-alhálózat átviteli zónák szabályozhatja a 2. rétegbeli hálózatok NSX-T. a alkalmazásfelhasználói ügyfélkörét
* **NsxtHostTransport** -VLAN-alhálózat gazdagép átviteli zóna.

### <a name="management-network-cidr-range-breakdown"></a>Felügyeleti hálózati CIDR-tartomány lebontása

a megadott vSphere/vSAN-alhálózatokat CIDR-tartományt több alhálózatra van felosztva.  Az alábbi táblázat az előtagok megengedett bontásához egy példát mutat be.  A példában **192.168.0.0** , a CIDR-tartományt.

Példa:

| A megadott vSphere/vSAN-alhálózatokat CIDR-előtagot | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Rendszerfelügyelet | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Gazdagép-átviteli NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Edge-átviteli NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| Edge Uplink1 NSX-T | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| Edge uplink2 NSX-T | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>További lépések

* [Virtuális helyi hálózatok és alhálózatok létrehozása és kezelése](https://docs.azure.cloudsimple.com/vlansubnet/)