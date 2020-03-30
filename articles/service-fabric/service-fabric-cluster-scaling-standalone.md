---
title: Az Azure Service Fabric önálló fürtméretezése
description: Ismerje meg a Service Fabric önálló fürtök méretezése, illetve ki és fel vagy le.
author: dkkapur
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 16ec0eb429ec6e8f6613490226b7cff01dff1b32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451911"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>A Szolgáltatásháló-háló önálló fürtjainak méretezése
A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. Egy gép vagy virtuális gép, amely egy fürt része, nevezzük csomópont. A fürtök potenciálisan több ezer csomópontot tartalmazhatnak. A Service Fabric-fürt létrehozása után vízszintesen (módosíthatja a csomópontok számát) vagy függőlegesen skálázhatja a fürtöt (módosíthatja a csomópontok erőforrásait).  A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön.  A fürt méretezése, az alkalmazások automatikusan skálázható is.

Miért érdemes átméretezni a fürtöt? Az alkalmazás igények idővel változnak.  Előfordulhat, hogy növelnie kell a fürt erőforrásait, hogy megfeleljen a megnövekedett alkalmazásterhelésnek vagy hálózati forgalomnak, vagy csökkentse a fürterőforrásait, ha az igény csökken.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Méretezés be- és kiméretezés, illetve vízszintes méretezés
Módosítja a csomópontjainak számát a fürtben.  Miután az új csomópontok csatlakoztak a fürthöz, a [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md) áthelyezi a szolgáltatásokat, amelyek csökkentik a meglévő csomópontok terhelését.  A csomópontok számát is csökkentheti, ha a fürt erőforrásait nem használja hatékonyan.  Ahogy a csomópontok elhagyják a fürtöt, a szolgáltatások elmozdulnak ezekről a csomópontokról, és a terhelés növekszik a fennmaradó csomópontokon.  Az Azure-ban futó fürt csomópontjaiszámának csökkentése pénzt takaríthat meg, mivel a használt virtuális gépek számáért fizet, és nem a virtuális gépek munkaterheléséért.  

- Előnyök: Végtelen skála, elméletben.  Ha az alkalmazás méretezhetőségre lett tervezve, további csomópontok hozzáadásával engedélyezheti a korlátlan növekedést.  A felhőalapú környezetekben használt eszközök megkönnyítik a csomópontok hozzáadását vagy eltávolítását, így könnyen módosíthatja a kapacitást, és csak a használt erőforrásokért kell fizetnie.  
- Hátrányok: Az alkalmazásokat [méretezhetőségre](service-fabric-concepts-scalability.md)kell tervezni.  Az alkalmazás-adatbázisok és a perzisztencia további architekturális munkát is igényelhet.  A Service Fabric állapotalapú szolgáltatásaimegbízható [gyűjtemények](service-fabric-reliable-services-reliable-collections.md) azonban sokkal könnyebbé teszik az alkalmazásadatok méretezését.

Az önálló fürtök lehetővé teszik a Service Fabric-fürt helyszíni vagy az Ön által választott felhőszolgáltatóban történő üzembe helyezését.  A csomóponttípusok fizikai gépekből vagy virtuális gépekből állnak, a központi telepítéstől függően. Az Azure-ban futó fürtökhöz képest az önálló fürt méretezése egy kicsit nagyobb szerepet jelent.  Manuálisan kell módosítania a csomópontok számát a fürtben, majd futtatnia kell egy fürtkonfigurációs frissítést.

A csomópontok eltávolítása több frissítést is kezdeményezhet. Egyes csomópontok `IsSeedNode=”true”` címkével vannak megjelölve, és a fürtjegyzék [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest)használatával történő lekérdezésével azonosíthatók. Az ilyen csomópontok eltávolítása hosszabb időt vehet igénybe, mint mások, mivel a magcsomópontokat ilyen esetekben mozgatni kell. A fürtnek legalább három elsődleges csomóponttípus-csomópontot kell fenntartania.

> [!WARNING]
> Azt javasoljuk, hogy ne csökkentse a csomópontszámát a fürt [megbízhatósági szintjének fürtmérete](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) alá. Ez zavarja a Service Fabric rendszerszolgáltatások replikálását a fürtön keresztül, és destabilizálja vagy esetleg megsemmisíti a fürtöt.
>

Önálló fürt méretezésekénél tartsa szem előtt az alábbi irányelveket:
- Az elsődleges csomópontok cseréjét egyik csomópontot a másik után kell elvégezni, ahelyett, hogy eltávolítaná, majd kötegekben hozzáadná őket.
- A csomóponttípus eltávolítása előtt ellenőrizze, hogy vannak-e olyan csomópontok, amelyek a csomóponttípusra hivatkoznak. Távolítsa el ezeket a csomópontokat a megfelelő csomóponttípus eltávolítása előtt. Miután az összes megfelelő csomópontot eltávolította, eltávolíthatja a NodeType típust a fürtkonfigurációból, és megkezdheti a konfigurációfrissítést a [Start-ServiceFabricConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)használatával.

További információt az [önálló fürt méretezése](service-fabric-cluster-windows-server-add-remove-nodes.md)című témakörben talál.

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Fel- és leskálázás vagy függőleges méretezés 
Módosítja a fürt csomópontjainak erőforrásait (CPU, memória vagy tároló).
- Előnyök: A szoftver- és alkalmazásarchitektúra ugyanaz marad.
- Hátrányok: Véges skálázás, mivel van egy korlát, hogy mennyit növelheti az erőforrásokat az egyes csomópontokon. Állásidő, mert az erőforrások hozzáadásához vagy eltávolításához fizikai vagy virtuális gépeket offline állapotba kell helyeznie.

## <a name="next-steps"></a>További lépések
* További információ az [alkalmazások méretezhetőségéről.](service-fabric-concepts-scalability.md)
* [Azure-fürt méretezése be- és ki.](service-fabric-tutorial-scale-cluster.md)
* [Az Azure-fürtöket programozott módon méretezzük](service-fabric-cluster-programmatic-scaling.md) a gördülékeny Azure-beli SDK használatával.
* [Önálló fürt méretezése be- és kiméretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

