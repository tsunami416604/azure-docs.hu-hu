---
title: Példa az Azure infrastruktúra-útmutatóra
description: Ismerje meg az Azure-beli infrastruktúra üzembe helyezésének legfontosabb tervezési és megvalósítási irányelveit.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 500de3f89bd041adf0b73e21762495d8c89e19c8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286291"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Példa Azure Infrastructure-útmutató Linux rendszerű virtuális gépekhez
Ez a cikk végigvezeti egy példaként szolgáló alkalmazás-infrastruktúra kiépítésén. Részletesen ismertetjük az infrastruktúra megtervezését egy egyszerű, online tároláshoz, amely összefoglalja az elnevezési konvenciók, a rendelkezésre állási csoportok, a virtuális hálózatok és a terheléselosztó, valamint a virtuális gépek (VM-EK) tényleges üzembe helyezésének összes irányelvét és döntését.

## <a name="example-workload"></a>Példa munkaterhelésre
Az Adventure Works-ciklusok egy online áruházbeli alkalmazást kívánnak létrehozni az Azure-ban, amely a következőket tartalmazza:

* Két Nginx-kiszolgáló, amely az ügyfél előtéreit futtatja egy webes rétegben
* Két Nginx-kiszolgáló, amely egy alkalmazási szinten dolgozza fel az adatfeldolgozást és a rendeléseket
* Két MongoDB-kiszolgáló egy többrétegű fürthöz, amely a termékek és megrendelések adatbázis-szinten történő tárolásához szükséges.
* Két Active Directory tartományvezérlő a hitelesítési szinten lévő vevői fiókok és szállítók számára
* Az összes kiszolgáló két alhálózatban található:
  * a webkiszolgálók előtér-alhálózata 
  * az alkalmazáskiszolgáló, a MongoDB-fürt és a tartományvezérlők háttérbeli alhálózata

![Az alkalmazás-infrastruktúra különböző szintjeinek ábrája](./media/infrastructure-example/example-tiers.png)

A bejövő biztonságos webes forgalomnak a webkiszolgálók közötti terheléselosztást kell biztosítania, mivel az ügyfelek böngészik az online áruházban. A webkiszolgálókról érkező HTTP-kérések formájában végzett feldolgozási forgalom elosztását az alkalmazás-kiszolgálók között kell elrendezni. Emellett az infrastruktúrát magas rendelkezésre álláshoz kell tervezni.

Az eredményül kapott kialakításnak a következőket kell tartalmaznia:

* Azure-előfizetés és-fiók
* Egyetlen erőforráscsoport
* Azure Managed Disks
* Virtuális hálózat két alhálózattal
* A hasonló szerepkörrel rendelkező virtuális gépek rendelkezésre állási csoportjai
* Virtual machines (Virtuális gépek)

A fenti elnevezési konvenciók mindegyike a következő:

* Az Adventure Works-ciklusok a **[it munkaterhelés]-[Location]-[Azure Resource]** előtagot használják.
  * Ebben a példában a "**azos**" (Azure on-line Store) az IT-munkaterhelés neve és a "**use**" (az USA 2. keleti régiója)
* Virtuális hálózatok AZOS használata – VN<strong>[Number]</strong>
* A rendelkezésre állási készletek a azos használatát használják **[role]**
* A virtuális gépek nevei azos használ-VM-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-előfizetések és-fiókok
Az Adventure Works-ciklusok nagyvállalati előfizetéssel rendelkeznek, amely az Adventure Works Enterprise előfizetését használja az IT-számítási feladatok számlázásához.

## <a name="storage"></a>Storage
Az Adventure Works-ciklusok határozzák meg, hogy az Azure Managed Disks-t használják. Virtuális gépek létrehozásakor a rendszer a tárterület rendelkezésre álló tárolási szintjein is használható:

* A webkiszolgálók, az alkalmazás-kiszolgálók és a tartományvezérlők, valamint az adatlemezek **szabványos tárolói** .
* **Prémium szintű Storage** a MongoDB és az adatlemezek számára.

## <a name="virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok
Mivel a virtuális hálózatnak nincs szüksége folyamatos kapcsolatra a kaland munkahelyi ciklusok helyszíni hálózatán, úgy döntöttek, hogy csak felhőalapú virtuális hálózaton vannak.

Egy csak felhőalapú virtuális hálózatot hoztak létre a következő beállításokkal a Azure Portal használatával:

* Név: AZOS-USE-VN01
* Hely: USA 2. keleti régiója
* Virtuális hálózati címtartomány: 10.0.0.0/8
* Első alhálózat:
  * Név: előtér
  * Címterület: 10.0.1.0/24
* Második alhálózat:
  * Name: háttérrendszer
  * Címterület: 10.0.2.0/24

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Az online áruház négy rétegének magas rendelkezésre állásának fenntartása érdekében az Adventure Works-ciklusok négy rendelkezésre állási csoportban vannak eldöntve:

* **azos – webes használat** a webkiszolgálók számára
* **azos-alkalmazás használata** az alkalmazáskiszolgáló esetében
* **azos –** a MongoDB-alapú fürtözött fürt kiszolgálóinak
* **azos –** tartományvezérlőként való használata a tartományvezérlők számára

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az Adventure Works-ciklusok az Azure-beli virtuális gépek következő nevein vannak eldöntve:

* **azos – virtuális gép – web01** az első webkiszolgálóhoz
* **azos – virtuális gép – web02** a második webkiszolgálóhoz
* **azos-use-VM-app01** az első alkalmazáskiszolgáló számára
* **azos-use-VM-app02** a második alkalmazás-kiszolgálóhoz
* **azos-use-VM-db01** a fürt első MongoDB-kiszolgálójához
* **azos – virtuális gép – db02** a fürt második MongoDB-kiszolgálójához
* **azos – a-VM-DC01 használata** az első tartományvezérlőhöz
* **azos – virtuális gép dc02** a második tartományvezérlőhöz

Itt látható az eredményül kapott konfiguráció.

![Az Azure-ban üzembe helyezett végleges alkalmazás-infrastruktúra](./media/infrastructure-example/example-config.png)

Ez a konfiguráció a következőket foglalja magában:

* Csak felhőalapú virtuális hálózat két alhálózattal (előtér és háttér)
* Azure Managed Disks a standard és a prémium szintű lemezek használatával
* Négy rendelkezésre állási csoport, egyet az online áruház egyes szintjeihez
* A négy rétegek virtuális gépei
* Külső elosztott terhelésű készlet a HTTPS-alapú webes forgalom számára az internetről a webkiszolgálókra
* Belső terheléselosztási készlet a webkiszolgálókról az alkalmazáskiszolgáló felé irányuló titkosítatlan webes forgalomhoz
* Egyetlen erőforráscsoport
