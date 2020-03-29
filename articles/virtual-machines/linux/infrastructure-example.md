---
title: Példa az Azure-infrastruktúra forgatókönyve
description: Ismerje meg a példainfrastruktúra Azure-beli üzembe helyezésére vonatkozó legfontosabb tervezési és megvalósítási irányelveket.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6040bf8862131f5a8a5564cd2f5d845fa0490a95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944906"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Példa az Azure-infrastruktúra forgatókönyve linuxos virtuális gépekhez
Ez a cikk végigvezeti egy példa alkalmazás-infrastruktúra kiépítése. Részletesen tervezzük meg egy egyszerű on-line áruház infrastruktúrájának tervezését, amely egyesíti az elnevezési konvenciókkal, a rendelkezésre állási készletekkel, a virtuális hálózatokkal és a terheléselosztókkal kapcsolatos összes irányelvet és döntést, valamint a virtuális gépek (VM-ek) tényleges üzembe helyezését.

## <a name="example-workload"></a>Példa munkaterhelésre
A Adventure Works Cycles egy on-line áruházi alkalmazást szeretne építeni az Azure-ban, amely a következőkből áll:

* Két nginx-kiszolgáló, amely az ügyfél előtér-kiszolgálóját futtatja egy webes rétegben
* Két nginx-kiszolgáló, amelyek adatokat és rendeléseket dolgoznak fel egy alkalmazásszinten
* Két MongoDB-kiszolgáló egy szilánkos fürt része a termékadatok és a rendelések adatbázisrétegben való tárolásához
* Két Active Directory-tartományvezérlő ügyfélfiókokhoz és szállítókhoz hitelesítési szinten
* Az összes kiszolgáló két alhálózatban található:
  * előtér-alhálózat a webkiszolgálókszámára 
  * háttér-alhálózat az alkalmazáskiszolgálókhoz, a MongoDB-fürthöz és a tartományvezérlőkhöz

![Az alkalmazásinfrastruktúra különböző szintjeinek diagramja](./media/infrastructure-example/example-tiers.png)

A bejövő biztonságos webes forgalmat a webkiszolgálók között terheléselosztásnak kell tekinteni, miközben az ügyfelek az on-line áruházban böngésznek. A webkiszolgálókról HTTP-kérelmek formájában küldött rendelésfeldolgozási forgalmat az alkalmazáskiszolgálók között terheléselosztással kell elbírni. Ezenkívül az infrastruktúrát magas rendelkezésre állásra kell megtervezni.

Az így kapott formatervezési mintának a következőket kell tartalmaznia:

* Azure-előfizetés és fiók
* Egyetlen erőforráscsoport
* Azure Managed Disks
* Két alhálózattal rendelkező virtuális hálózat
* A hasonló szerepkörrel rendelkező virtuális gépek rendelkezésre állási készletei
* Virtuális gépek

A fentiek mindegyike követi az alábbi elnevezési konvenciókat:

* A Adventure Works Cycles előtagként az **[INFORMATIKAI munkaterhelést]-[hely]-[Azure-erőforrás]** használja előtagként
  * Ebben a példában az "**azos**" (Azure On-line Store) az informatikai számítási feladatok neve és a "**use**" (USA keleti része 2) az a hely,
* Virtuális hálózatok használata AZOS-USE-VN<strong>[szám]</strong>
* Az elérhetőségi csoportok az azos-use-as-**[role]**
* A virtuálisgép-nevek azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-előfizetések és fiókok
A Adventure Works Cycles a Nagyvállalati előfizetését, az Adventure Works Enterprise Subscription-t használja az informatikai munkaterhelés számlázásának biztosításához.

## <a name="storage"></a>Storage
A Kalandor-works ciklusok meghatározták, hogy az Azure felügyelt lemezeit kell használniuk. Virtuális gépek létrehozásakor mindkét rendelkezésre álló tárterület-szintet használja a rendszer:

* **A** webkiszolgálók, az alkalmazáskiszolgálók, a tartományvezérlők és adatlemezeik szabványos tárolása.
* **Prémium szintű tárhely** a MongoDB sharded fürtkiszolgálók és adatlemezeik számára.

## <a name="virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok
Mivel a virtuális hálózatnak nincs szüksége folyamatos kapcsolatra a helyszíni kalandmunka-ciklusokkal, úgy döntöttek, hogy csak felhőalapú virtuális hálózatot hoznak létre.

Az Azure Portalon a következő beállításokkal hoztak létre csak felhőalapú virtuális hálózatot:

* Név: AZOS-USE-VN01
* Helyszín: USA keleti része 2
* Virtuális hálózati címtér: 10.0.0.0/8
* Első alhálózat:
  * Név: Előtér
  * Címterület: 10.0.1.0/24
* Második alhálózat:
  * Név: Háttérnév
  * Címterület: 10.0.2.0/24

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Annak érdekében, hogy az on-line áruház mind a négy szintjének magas rendelkezésre állását fenn tartsa, a Kalandor-ciklusok négy rendelkezésre állási csoportról döntöttek:

* **azos-use-as-web** a web szerverek
* **azos-use-as-app** az alkalmazás szerverek
* **azos-use-as-db** a MongoDB szilánkos fürt kiszolgálóihoz
* **azos-use-as-dc** tartományvezérlőkhöz

## <a name="virtual-machines"></a>Virtuális gépek
A Kalandor-worksciklusok az alábbi neveket határozta meg az Azure-beli virtuális gépekhez:

* **azos-use-vm-web01** az első webszerver
* **azos-use-vm-web02** a második webszerverhez
* **azos-use-vm-app01** az első alkalmazáskiszolgálóhoz
* **azos-use-vm-app02** a második alkalmazáskiszolgálóhoz
* **azos-use-vm-db01** a fürt első MongoDB kiszolgálójához
* **azos-use-vm-db02** a fürt második MongoDB kiszolgálójára
* **azos-use-vm-dc01** az első tartományvezérlőhöz
* **azos-use-vm-dc02** a második tartományvezérlőhöz

Itt van az eredményül kapott konfiguráció.

![Az Azure-ban telepített végső alkalmazásinfrastruktúra](./media/infrastructure-example/example-config.png)

Ez a konfiguráció a következőket foglalja magában:

* Csak felhőalapú virtuális hálózat két alhálózattal (Előtér és Háttérrendszer)
* Azure felügyelt lemezek standard és prémium szintű lemezek használatával
* Négy rendelkezésre állási csoport, egy az on-line áruház minden rétegére
* A négy réteg virtuális gépei
* Külső terheléselosztásos készlet https-alapú webes forgalomhoz az internetről a webkiszolgálókra
* Belső terheléselosztási készlet a webkiszolgálókról az alkalmazáskiszolgálókra irányuló titkosítatlan webes forgalomhoz
* Egyetlen erőforráscsoport
