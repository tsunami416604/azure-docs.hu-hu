---
title: "Tesztelhetőségi: Szolgáltatás kommunikációs |} Microsoft Docs"
description: "Szolgáltatások közötti kommunikáció az olyan kritikus integrációs pont Service Fabric-alkalmazás. A cikk ismerteti a tervezési szempontokat és tesztelési módszereket."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4c6b53673dd4c79ce435c1593e7d08c7dd4a1cb0
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>A Service Fabric tesztelhetőségi forgatókönyvek: kommunikációs szolgáltatás
Mikroszolgáltatások létrehozására és természetesen az Azure Service Fabric architekturális stílusok szolgáltatásorientált felülete. Az elosztott-architektúrák típusai componentized mikroszolgáltatási alkalmazások általában több szolgáltatás egymással kapcsolatot igénylő álló. Még a legegyszerűbb esetben általában van legalább egy állapot nélküli webszolgáltatás és az állapot-nyilvántartó tárolási ADATSZOLGÁLTATÁSNÁL, amely kell kommunikálniuk.

Szolgáltatások közötti kommunikáció nem egy alkalmazást, az kritikus integrációs pont, mert minden egyes szolgáltatás elérhetővé teszi a távoli API más szolgáltatások. API határok olyan készlete, amelyek i/o általában végzett néhány eljárni, tesztelése és érvényesítési megfelelő mennyiségű igényel.

Nincsenek abban az esetben, ha a szolgáltatás határok együtt vezetékes elosztott rendszer számos szempontok:

* *Átviteli protokoll*. Fog alkalmazni a megnövekedett való együttműködés HTTP, vagy egy egyéni bináris protokoll maximális átviteli sebesség eléréséhez?
* *Hibakezelés*. Állandó és átmeneti hibák kezelésének módját? Mi történik, ha a szolgáltatás egy másik csomópont?
* *Időtúllépések és a késleltetés*. Állással alkalmazásokban hogyan minden szolgáltatási réteg kezelnek várakozási ideje a vermen keresztül, és a felhasználó számára?

Hogy a Service Fabric által biztosított beépített szolgáltatás kommunikációs összetevők valamelyikét használja, vagy hoz létre a saját, a szolgáltatások közötti kapcsolat tesztelése fontos a rugalmasságot az alkalmazás biztosítja.

## <a name="prepare-for-services-to-move"></a>Készítse elő a szolgáltatások áthelyezése
Előfordulhat, hogy idővel Navigálás szolgáltatáspéldány. Ez akkor különösen igaz olyan esetben, ha vannak konfigurálva az optimális erőforrás egyéni szabott terheléselosztás betöltési metrikáit. A Service Fabric helyezi át a szolgáltatáspéldány azok rendelkezésre állásának maximalizálását frissítések, a feladatátvétel, a kibővített és a más elosztott rendszer élettartamuk során előforduló helyzetek esetén.

Szolgáltatások Navigálás a fürt, mivel az ügyfelek és egyéb szolgáltatások szeretné kezelni a két forgatókönyv akkor kérdezze meg a szolgáltatás kell előkészíteni:

* A szolgáltatás példányt, vagy a partíció replika óta volt szó, ha azt át lett helyezve. Ez a szolgáltatási életciklus részét, és azt kell várt, és az alkalmazás élettartama során.
* A szolgáltatás példányt, vagy a partíció replikája éppen áthelyezi. A szolgáltatás egyik csomópontról egy másikra feladatátvételi nagyon gyorsan Service Fabric történik, bár lehet késleltetést a rendelkezésre állási Ha ez az összetevő a szolgáltatás elindításához lassú.

Ezek a forgatókönyvek szabályosan kezelése fontos smooth futó rendszer esetén. Ehhez a következőket kell figyelembe venni, hogy:

* Minden szolgáltatás, amely képes csatlakozni egy *cím* (például a HTTP vagy a websocket elemek) figyelő. Ha egy szolgáltatáspéldány vagy partíció helyezi, a cím végpont változik. (Áthelyezi azt egy másik csomópont egy másik IP-címmel.) A beépített kommunikációs összetevők használata, azok újra feloldó szolgáltatás címek meg fogja kezelni.
* Lehet, a szolgáltatás példány elindul felfelé a figyelő a szolgáltatás várakozási ideje ideiglenes növelése újra. Ez attól függ, hogy milyen gyorsan a szolgáltatás megjelenik-e a figyelő a szolgáltatáspéldány áthelyezése után.
* A meglévő kapcsolatokat kell zárni, majd újra megnyitni a szolgáltatás egy új csomópont megnyitása után. Egy szabályos csomópont leállítása vagy újraindítása lehetővé teszi, hogy a meglévő kapcsolatok kell leállítása idő.

### <a name="test-it-move-service-instances"></a>Tesztelheti: szolgáltatáspéldány áthelyezése
A Service Fabric tesztelhetőségi eszközök segítségével egy teszt forgatókönyv tesztelésére ezekben a helyzetekben különböző módon hozhat létre:

1. Helyezze át egy állapotalapú szolgáltatás elsődleges másodpéldány.
   
    Az elsődleges másodpéldány állapotalapú szolgáltatási partíció áthelyezhető okból sem állhat. Ezzel a cél egy adott partícióra megtekintéséhez, hogy a szolgáltatások reagálnak az áthelyezés nagyon szabályozott módon elsődleges replikáját.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Csomópont leállítása.
   
    Egy csomópont leáll, amikor a Service Fabric helyez át valamennyi szolgáltatás példányainak és partíciók, melyeket a fürt más elérhető csomópontok közül a csomóponton. Ezzel a teszt olyan helyzet, ha egy csomópont a fürt és a szolgáltatáspéldány összes elvesztését, és ezen a csomóponton replikának történő áthelyezéséhez.
   
    A PowerShell segítségével leállíthatja egy csomópont **Stop-ServiceFabricNode** parancsmagot:
   
    ```powershell
   
    PS > Restart-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Szolgáltatás rendelkezésre állása karbantartása
Platformként a Service Fabric célja, hogy a szolgáltatások magas rendelkezésre állás biztosításához. De szélsőséges esetben alapul szolgáló infrastruktúra problémákat is okozhatnak elérhetetlensége. Fontos túl tesztelése, forgatókönyvek esetén.

Állapotalapú szolgáltatások a kvórum rendszerbe használja replikálja a magas rendelkezésre állás állapota. Ez azt jelenti, hogy a kvórum replikák kell írási műveletek végrehajtásához. Bizonyos ritkán előforduló esetekben, például a széles körű hardverhiba replikák kvórum nem érhetők el. Ezekben az esetekben pedig nem fogják tudni írási műveletek végrehajtására, de továbbra is lesz az olvasási műveletek végrehajtásához.

### <a name="test-it-write-operation-unavailability"></a>Tesztelheti: írási művelet elérhetetlensége
A Service Fabric a tesztelhetőségi eszközök használatával helyezhet el a hibát, amely a kvórum elvesztése, mert egy tesztelési kapott. Bár az ilyen esetben nem ritka, fontos, hogy az ügyfelek és az állapotalapú service szolgáltatástól függő szolgáltatások előkészített kezelésére, ha azok nem hajtható végre írási kérelmeket szolgál, hogy amikor. Fontos továbbá, hogy az állapotalapú szolgáltatás felismeri ezt a lehetőséget, és lehet szabályosan tájékoztatni a hívóknak.

A PowerShell használatával lehet szükség a kvórum elvesztése **Invoke-ServiceFabricPartitionQuorumLoss** parancsmagot:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Ebben a példában hivatott `QuorumLossMode` való `QuorumReplicas` annak jelzésére, hogy azt szeretnénk, hogy kvórum elvesztése nélkül összes replika le. Ezzel a módszerrel az olvasási műveletek is továbbra is lehetséges. Egy olyan forgatókönyvet, amikor nem érhető el egy teljes partíció teszteléséhez állíthat be erre a kapcsolóra `AllReplicas`.

## <a name="next-steps"></a>Következő lépések
[További tudnivalók tesztelhetőségi műveletek](service-fabric-testability-actions.md)

[További tudnivalók tesztelhetőségi forgatókönyvek](service-fabric-testability-scenarios.md)

