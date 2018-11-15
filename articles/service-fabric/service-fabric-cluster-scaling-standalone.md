---
title: Az Azure Service Fabric önálló fürt méretezése |} A Microsoft Docs
description: Tudnivalók a Service Fabric-fürtök különálló a, vagy ki, és felfelé vagy lefelé méretezés.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: ryanwi
ms.openlocfilehash: cbd8374e055d1bb9781990f70ed42ae5d5a5ad9b
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634703"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Önálló Service Fabric-fürtök méretezése
Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része csomópontoknak nevezzük. Fürtök potenciálisan több ezer csomóponton is tartalmazhat. Egy Service Fabric-fürt létrehozását követően, horizontálisan a fürt (módosíthatja a csomópontok számát), vagy függőlegesen (módosíthatja a csomópontok az erőforrások).  Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak.  A fürt skálázható, mivel az alkalmazások automatikus méretezése is.

A fürt átméretezése miért? Alkalmazások számára az idő előrehaladtával változik.  Szükség lehet növelni a fürterőforrások felel meg az alkalmazás nagyobb számítási feladatok vagy a hálózati forgalom, vagy csökkentse a fürterőforrások, ha az igény csökken.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skálázás be és ki, vagy a horizontális skálázás
Módosíthatja a fürtben található csomópontok számát.  Miután az új csomópontok csatlakoztatását a fürthöz, a [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md) szolgáltatások helyezi őket, ami csökkenti a meglévő csomópontok terhelése.  A csomópontok számát is csökkentheti, ha a fürt erőforrásait nem hatékonyan használ.  Csomópontok hagyja a fürt, szolgáltatások áthelyezése ki azokat a csomópontokat, és a terhelés növekedésével a többi csomóponton.  Egy Azure-ban futó fürtben található csomópontok számának csökkentése pénzt takaríthat meg, mivel után kell fizetni, a virtuális gépek száma, használata és a virtuális gépeken a munkaterhelés nem.  

- Előnyök: Korlátlan méretű, elméletileg.  Ha az alkalmazás t méretezhetőségre tervezték, korlátlan növekedési további fürtcsomópontok hozzáadásával engedélyezheti.  Az eszközkészlet a felhőalapú környezetek megkönnyíti a hozzáadása vagy eltávolítása a csomópontokra, így egyszerűen módosíthatja a kapacitást, és csak kell fizetnie az erőforrások után.  
- Hátrányok: Csak azok az alkalmazások [méretezhetőséghez tervezett](service-fabric-concepts-scalability.md).  Alkalmazás-adatbázisok és az adatmegőrzés szükség lehet további architekturális munka, valamint méretezését.  [A Reliable collections](service-fabric-reliable-services-reliable-collections.md) a Service Fabric állapotalapú szolgáltatások azonban sokkal könnyebb méretezését, az alkalmazásadatok.

Önálló fürtök lehetővé teszik, hogy a helyszíni üzembe helyezése a Service Fabric fürt vagy a felhőbeli szolgáltató a választott.  A csomóponttípusok fizikai gépek vagy a virtuális gépek, a telepítéstől függően állnak. Azure-ban futó fürtök képest, különálló fürt méretezését valamivel több jelentőséggel bír.  Meg kell a fürtben található csomópontok számát manuálisan módosíthatja, és futtassa a fürt konfiguráció frissítése.

Csomópont eltávolítása több frissítés kezdeményezhet. Egyes csomópontok lesznek megjelölve `IsSeedNode=”true”` címkézés és a fürt lekérdezésével azonosíthatók manifest használatával [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Ilyen csomópont eltávolítása, mint a többi tovább tarthat, mivel az ilyen esetekben áthelyezhető a magcsomópontok tartalmaz. A fürt legalább három elsődleges típusú csomópontok kell karbantartása.

> [!WARNING]
> Azt javasoljuk, hogy az alábbi csomópontok száma nem csökkenti a [fürt méretét a megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) a fürt számára. Ez zavarja a megfelelő kezelésének biztosítása a Service Fabric rendszer Services replikálni a fürtön, és fog történő leállítása instabillá vagy esetleg semmisítse meg a fürtöt.
>

Különálló fürt vertikális, tartsa szem előtt az alábbi irányelveket:
- Váltja fel az elsődleges csomópont lehet elvégezni egy csomópont eltávolítása, majd kötegekben helyett egymás után.
- Mielőtt eltávolítaná a csomópont típusa, annak ellenőrzése, hogy azokat a csomópontokat, a csomópont típusa hivatkozik. Ezek a csomópontok eltávolítása a megfelelő csomóponttípus eltávolítása előtt. Miután az összes megfelelő csomópont el lesznek távolítva, a NodeType csomóponttípus eltávolítása a fürt konfigurációját, és a konfigurálás megkezdése használó frissítse [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

További információkért lásd: [önálló fürt méretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Felfelé és lefelé skálázást, vagy a vertikális skálázás 
Az erőforrások (CPU, memória, vagy tárolási) a fürtben található csomópontok változik.
- Előnyök: Szoftver és az alkalmazásarchitektúrát változatlan marad.
- Hátrányok: Véges méretezhető, mivel az IP-címek fenntartási, növelheti az egyes csomópontokon erőforrások korlátozva van. Állásidő, mert szüksége lesz a fizikai vagy virtuális gépek offline annak érdekében, hogy erőforrásainak hozzáadása vagy eltávolítása.

## <a name="next-steps"></a>További lépések
* Ismerje meg [alkalmazás méretezhetőségi](service-fabric-concepts-scalability.md).
* [Egy Azure-fürtön lévő vagy horizontális skálázása](service-fabric-tutorial-scale-cluster.md).
* [Egy Azure-fürtön programozott skálázása](service-fabric-cluster-programmatic-scaling.md) az fluent Azure compute SDK-t.
* [Vagy önálló fürtök skálázásának](service-fabric-cluster-windows-server-add-remove-nodes.md).

