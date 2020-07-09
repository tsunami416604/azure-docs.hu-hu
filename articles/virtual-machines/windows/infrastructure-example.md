---
title: Példa az Azure infrastruktúra-útmutatóra
description: Ismerje meg az Azure-beli infrastruktúra üzembe helyezésének legfontosabb tervezési és megvalósítási irányelveit.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: example-scenario
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43e96b891e60dfcf8bc3c29b202bb60213905372
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81869465"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Példa Azure Infrastructure-útmutató Windows rendszerű virtuális gépekhez
Ez a cikk végigvezeti egy példaként szolgáló alkalmazás-infrastruktúra kiépítésén. Részletesen ismertetjük az infrastruktúra tervezését egy egyszerű online áruházban, amely összefoglalja az elnevezési konvenciókat, a rendelkezésre állási csoportokat, a virtuális hálózatokat és a Load balancereket, és ténylegesen üzembe helyezi a virtuális gépeket (VM).

## <a name="example-workload"></a>Példa munkaterhelésre
Az Adventure Works-ciklusok egy online áruházbeli alkalmazást kívánnak létrehozni az Azure-ban, amely a következőket tartalmazza:

* Két IIS-kiszolgáló, amely az ügyfél előtérét futtatja egy webes rétegben
* Két IIS-kiszolgáló, amely egy alkalmazási szinten dolgozza fel az adatfeldolgozást és a rendeléseket
* Két Microsoft SQL Server példány az alAlwaysOnon rendelkezésre állási csoportokkal (két SQL-kiszolgáló és egy tanúsító többségi csomópont) a termékek és megrendelések adatbázis-rétegbeli tárolásához
* Két Active Directory tartományvezérlő a hitelesítési szinten lévő vevői fiókok és szállítók számára
* Az összes kiszolgáló két alhálózatban található:
  * a webkiszolgálók előtér-alhálózata 
  * az alkalmazás-kiszolgálók, az SQL-fürt és a tartományvezérlők háttérbeli alhálózata

![Az alkalmazás-infrastruktúra különböző szintjeinek ábrája](./media/infrastructure-example/example-tiers.png)

A bejövő biztonságos webes forgalomnak a webkiszolgálók között terheléselosztást kell biztosítania az online áruházban. A webkiszolgálókról érkező HTTP-kérések formájában végzett feldolgozási forgalomnak egyensúlyban kell lennie az alkalmazás-kiszolgálók között. Emellett az infrastruktúrát magas rendelkezésre álláshoz kell tervezni.

Az eredményül kapott kialakításnak a következőket kell tartalmaznia:

* Azure-előfizetés és-fiók
* Egyetlen erőforráscsoport
* Azure Managed Disks
* Virtuális hálózat két alhálózattal
* A hasonló szerepkörrel rendelkező virtuális gépek rendelkezésre állási csoportjai
* Virtual machines (Virtuális gépek)

A fenti elnevezési konvenciók mindegyike a következő:

* Az Adventure Works-ciklusok a **[it munkaterhelés]-[Location]-[Azure Resource]** előtagot használják.
  * Ebben a példában a "**azos**" (Azure online áruház) az IT-munkaterhelés neve és a "**use**" (az USA 2. keleti régiója)
* Virtuális hálózatok AZOS használata – VN **[Number]**
* A rendelkezésre állási készletek a azos használatát használják **[role]**
* A virtuális gépek nevei azos használ-VM-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-előfizetések és-fiókok
Az Adventure Works-ciklusok nagyvállalati előfizetéssel rendelkeznek, amely az Adventure Works Enterprise előfizetését használja az IT-számítási feladatok számlázásához.

## <a name="storage"></a>Storage
Az Adventure Works-ciklusok határozzák meg, hogy az Azure Managed Disks-t használják. Virtuális gépek létrehozásakor a rendszer mindkét rendelkezésre álló tárolási szintet használja:

* A webkiszolgálók, az alkalmazás-kiszolgálók és a tartományvezérlők, valamint az adatlemezek **szabványos tárolói** .
* **Premium Storage** a SQL Server virtuális gépek és az adatlemezek számára.

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
Az online áruház mind a négy szintje magas rendelkezésre állásának fenntartása érdekében az Adventure Works-ciklusok négy rendelkezésre állási csoporton vannak eldöntve:

* **azos – webes használat** a webkiszolgálók számára
* **azos-alkalmazás használata** az alkalmazáskiszolgáló esetében
* **azos –** SQL Server-SQL-kiszolgálók használata
* **azos –** tartományvezérlőként való használata a tartományvezérlők számára

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az Adventure Works-ciklusok az Azure-beli virtuális gépek következő nevein vannak eldöntve:

* **azos – virtuális gép – web01** az első webkiszolgálóhoz
* **azos – virtuális gép – web02** a második webkiszolgálóhoz
* **azos-use-VM-app01** az első alkalmazáskiszolgáló számára
* **azos-use-VM-app02** a második alkalmazás-kiszolgálóhoz
* **azos-use-VM-sql01** a fürt első SQL Server kiszolgálójához
* **azos – a virtuális gép sql02** a fürt második SQL Server kiszolgálójához
* **azos – a-VM-DC01 használata** az első tartományvezérlőhöz
* **azos – virtuális gép dc02** a második tartományvezérlőhöz

Itt látható az eredményül kapott konfiguráció.

![Az Azure-ban üzembe helyezett végleges alkalmazás-infrastruktúra](./media/infrastructure-example/example-config.png)

Ez a konfiguráció a következőket foglalja magában:

* Csak felhőalapú virtuális hálózat két alhálózattal (előtér és háttér)
* Azure Managed Disks standard és prémium szintű lemezekkel
* Négy rendelkezésre állási csoport, egyet az online áruház egyes szintjeihez
* A négy rétegek virtuális gépei
* Külső elosztott terhelésű készlet a HTTPS-alapú webes forgalom számára az internetről a webkiszolgálókra
* Belső terheléselosztási készlet a webkiszolgálókról az alkalmazáskiszolgáló felé irányuló titkosítatlan webes forgalomhoz
* Egyetlen erőforráscsoport
