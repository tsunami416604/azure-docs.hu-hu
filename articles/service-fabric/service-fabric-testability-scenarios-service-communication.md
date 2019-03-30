---
title: 'Testability: Szolgáltatás kommunikációs |} A Microsoft Docs'
description: Szolgáltatások közötti kommunikáció nem Service Fabric-alkalmazás kritikus fontosságú integrációs pont. Ez a cikk ismerteti a tervezési szempontjai és tesztelési módszereket.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 529c8d74b6e0a63a7969f31d5b5e8073ecb79411
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665746"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>A Service Fabric testability alkalmazási helyzetek: A szolgáltatások közötti kommunikáció
Mikroszolgáltatások és architekturális stílusok szolgáltatásorientált felülete természetes módon az Azure Service Fabricben. Az elosztott architektúrák az ilyen típusú componentized épülő alkalmazások általában több szolgáltatás, amely egymáshoz beszélnie állnak. Még a legegyszerűbb esetben általában rendelkezik legalább egy állapot nélküli webes szolgáltatás és a egy állapotalapú tárolási szolgáltatás, amely kell kommunikálniuk.

Szolgáltatások közötti kommunikáció egy alkalmazás egy kritikus fontosságú integrációs pontot azért minden egyes szolgáltatás más szolgáltatások egy távoli API-t tesz elérhetővé. Működő API határokat egy készletét, amely általában magában foglalja az i/o-néhány ellátás, kellő számú tesztelés és ellenőrzés szükséges.

Számos szempontot győződjön meg arról, amikor ezen szolgáltatások határait a vezetékes együtt az elosztott rendszerekben:

* *Átviteli protokoll*. Fog használni a megnövekedett együttműködés HTTP-, vagy egy egyéni bináris protokoll maximális átviteli sebesség?
* *Hibakezelés*. Állandó és átmeneti hibák kezelésének módját? Mi történik, ha egy szolgáltatás helyezi át egy másik csomópont?
* *Időtúllépések és a késleltetés*. Az alkalmazásokban. állással hogyan fogja minden szolgáltatási réteg kezelni késés a verem és a felhasználó számára?

E egy-egy Service Fabric által biztosított beépített szolgáltatás kommunikációs összetevőjét használhatja, vagy létrehozhat egy saját, tesztelés, a szolgáltatások közötti interakciókat biztosítva rugalmasságot az alkalmazásban.

## <a name="prepare-for-services-to-move"></a>Szolgáltatások áthelyezése előkészítése
Előfordulhat, hogy idővel Navigálás szolgáltatáspéldányok. Ez akkor különösen igaz, ha vannak konfigurálva az optimális erőforrás szabott egyéni terheléselosztási terhelési mérőszámok. A Service Fabric áthelyezi a szolgáltatáspéldányok a rendelkezésre állás maximalizálása érdekében a frissítéseket, folyamatban lévő feladatátvételi teszteket, horizontális felskálázás és más helyzetekben egy elosztott rendszer élettartama során előforduló során is.

Szolgáltatások Navigálás a fürtben, mint az ügyfelek és egyéb szolgáltatások fel kell készülnöm szeretné kezelni a két esetben beszélnek szolgáltatáshoz:

* A szolgáltatás-példány vagy partíció replikája az utolsó óta, akkor azt már át lett helyezve. Ez egy normál része egy szolgáltatás-életciklusának, és az alkalmazás teljes élettartama során számíthat.
* A szolgáltatás-példány vagy partíció replikája áthelyezése folyamatban van. Egyik csomópontról a másikra szolgáltatás feladatátvétele Service Fabric nagyon gyorsan történik, bár előfordulhat késleltetés rendelkezésre állási Ha ez az összetevő a szolgáltatás indítása lassú.

Ezek a forgatókönyvek szabályosan kezelése fontos az smooth futó rendszerek. Ha igen, vegye figyelembe, hogy:

* Minden szolgáltatás, amely lehet csatlakoztatni egy *cím* , amely figyeli (például a HTTP vagy a websockets protokoll). Ha egy szolgáltatáspéldány vagy partíció helyezi, a cím végpontjainak módosítása. (Ez áthelyezi egy másik csomópont egy másik IP-címmel.) A beépített kommunikációs összetevők használata, azok újra feloldó szolgáltatás címek meg fogja kezelni.
* Előfordulhat, hogy lehet a szolgáltatások adatelérési idejére, a szolgáltatás-példány elindul felfelé a figyelő ideiglenes növekedése újra. Ez attól függ, hogy mennyi idő alatt a szolgáltatás a figyelő követően megnyílik a szolgáltatás példánya kerül.
* Minden olyan meglévő kapcsolatokat kell zárni és újra megnyitja a szolgáltatás egy új csomópont megnyitása után kell. Meglévő kapcsolatok szabályosan leállítani időt hagy az normális csomópont meghibásodásakor vagy újraindítás.

### <a name="test-it-move-service-instances"></a>Tesztelje azt: Helyezze át a szolgáltatáspéldányok
A Service Fabric testability eszközök segítségével egy teszt forgatókönyv teszteléséhez ezekben a helyzetekben különböző módon hozhat létre:

1. Helyezze át egy állapotalapú szolgáltatás elsődleges replikája.
   
    Az elsődleges replika állapotalapú szolgáltatás partíció áthelyezhetők a számtalan. Ezzel az elsődleges másodpéldány egy adott partíció megtekintéséhez, hogy a szolgáltatások reagálás az áthelyezés nagyon ellenőrzött módon célozza.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Csomópont leállítása.
   
    Amikor egy csomópont leáll, a Service Fabric összes szolgáltatáspéldányok vagy partíció, amely a csomóponton, hogy a fürt más elérhető csomópontok egyikét is helyezi. Használja ezt a helyzetet, ahol egy csomópontot a fürtből, valamint a szolgáltatáspéldányok összes elvesztését, és ezen a csomóponton replikák át kell helyeznie teszteléséhez.
   
    A PowerShell segítségével leállíthatja egy csomópont **Stop-ServiceFabricNode** parancsmagot:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Szolgáltatás elérhetőségének kezelése
-Platformként a Service Fabric célja, hogy a szolgáltatások magas rendelkezésre állást biztosít. De a szélsőséges esetben alapjául szolgáló infrastruktúrával kapcsolatos problémák is okozhatnak elérhetetlensége. Fontos, ebben az esetben túl teszteléséhez.

Állapotalapú szolgáltatások egy kvórum-alapú rendszer használatával replikálni a magas rendelkezésre állási állapotát. Ez azt jelenti, hogy a kvórum replikák kell írási műveletek végrehajtásához. Bizonyos ritkán előforduló esetekben, például egy széles körű hardverhiba replikák kvórum előfordulhat, hogy nem érhető el. Ezekben az esetekben nem lesz írási műveletek végrehajtására, de Ön továbbra is képesek lesznek az olvasási műveletek végrehajtásához.

### <a name="test-it-write-operation-unavailability"></a>Tesztelje azt: Írási művelet elérhetetlensége
A testability eszközökkel a Service Fabricben, szúr be egy tartalék, melynek kvórum elvesztése teszteléshez használhat. Ilyen esetben nem ritka, fontos, hogy az ügyfelek és a egy állapotalapú szolgáltatás függő szolgáltatások kezelésére, ahol nem vállalnak írási kérelmeket, amikor készek. Fontos továbbá, hogy maga az állapotalapú szolgáltatás ismeri ezt a lehetőséget, és biztonságosan kommunikálhatnak, a hívó.

A PowerShell használatával is idéz elő kvórum elvesztése **Invoke-ServiceFabricPartitionQuorumLoss** parancsmagot:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Ebben a példában beállított `QuorumLossMode` való `QuorumReplicas` jelzi, hogy szeretnénk idéz elő a kvórum elvesztése összes replika eltávolítása nélkül. Így az olvasási műveletek így is előfordulhatnak. Hol érhető el egy teljes partíciót a forgatókönyv teszteléséhez is beállíthatja ezt a kapcsolót `AllReplicas`.

## <a name="next-steps"></a>További lépések
[További tudnivalók a testability műveletek](service-fabric-testability-actions.md)

[További tudnivalók a testability alkalmazási helyzetek](service-fabric-testability-scenarios.md)

