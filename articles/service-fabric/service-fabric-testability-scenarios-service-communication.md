---
title: 'Testability: Szolgáltatás kommunikáció'
description: A szolgáltatás-szolgáltatás kommunikáció egy Service Fabric-alkalmazás kritikus integrációs pontja. Ez a cikk ismerteti a tervezési szempontokat és tesztelési technikákat.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 87b922cb9655588a22c739d26c9ce9e49d35781a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465559"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>A Service Fabric testability forgatókönyvei: Szolgáltatáskommunikáció
A mikroszolgáltatások és a szolgáltatásorientált architekturális stílusok természetesen felszínre kerülnek az Azure Service Fabricben. Az ilyen típusú elosztott architektúrák, komponenses mikroszolgáltatási alkalmazások általában állnak több szolgáltatás, amelyek egymással kell beszélni. Még a legegyszerűbb esetekben is általában legalább egy állapotmentes webszolgáltatással és egy állapotalapú adattárolási szolgáltatással rendelkezik, amelynek kommunikálnia kell.

A szolgáltatás-szolgáltatás kommunikáció egy alkalmazás kritikus integrációs pontja, mivel minden szolgáltatás távoli API-t tesz elérhetővé más szolgáltatások számára. Az I/O-t magában foglaló API-határok készletének megfelelő munka általában némi törődést igényel, jó mennyiségű teszteléssel és ellenőrzéssel.

Számos szempontot kell figyelembe venni, ha ezek a szolgáltatáshatárok egy elosztott rendszerben vannak összekötve:

* *Átviteli protokoll*. Http-t fog használni a nagyobb együttműködés érdekében, vagy egy egyéni bináris protokollt a maximális átviteli teljesítmény érdekében?
* *Hibakezelés*. Hogyan kezelik az állandó és átmeneti hibákat? Mi történik, ha egy szolgáltatás egy másik csomópontra költözik?
* *Időmegtetések és késés*. A többszintű alkalmazásokban hogyan kezeli az egyes szolgáltatásrétegek a késést a veremen és a felhasználón keresztül?

Akár a Service Fabric által biztosított beépített szolgáltatáskommunikációs összetevők et használja, akár sajátot hoz létre, a szolgáltatások közötti interakciók tesztelése elengedhetetlen az alkalmazás rugalmasságának biztosításához.

## <a name="prepare-for-services-to-move"></a>Felkészülés a szolgáltatások áthelyezésére
A szolgáltatáspéldányok idővel mozoghatnak. Ez különösen akkor igaz, ha a terhelési metrikák egyéni legszemélyre szabott optimális erőforrás-kiegyensúlyozás konfigurálása. A Service Fabric áthelyezi a szolgáltatáspéldányokat, hogy maximalizálja azok rendelkezésre állását a frissítések, a feladatátvételek, a horizontális felskálázás és más, az elosztott rendszer élettartama alatt előforduló helyzetek során is.

Ahogy a szolgáltatások mozognak a fürtben, az ügyfeleknek és más szolgáltatásoknak készen kell állniuk két forgatókönyv kezelésére, amikor egy szolgáltatással beszélnek:

* A szolgáltatáspéldány vagy partíció replika költözött, mivel a legutóbbi beszélt vele. Ez egy normál része a szolgáltatás életciklusa, és várhatóan az alkalmazás élettartama alatt történik.
* A szolgáltatáspéldány vagy partíció replika áthelyezése folyamatban van. Bár a szolgáltatás egyik csomópontról a másikra történő feladatátvétel nagyon gyorsan előfordul a Service Fabric, előfordulhat, hogy a rendelkezésre állás imteheti, ha a szolgáltatás kommunikációs összetevője lassan indul el.

Ezek a forgatókönyvek kezelése kecsesen fontos a zökkenőmentesen működő rendszer. Ehhez ne feledje, hogy:

* Minden szolgáltatás, amely csatlakoztatható van egy *címet,* amely figyeli a (például HTTP vagy WebSockets). Amikor egy szolgáltatáspéldány vagy partíció mozog, a címvégpont ja. (Egy másik, másik IP-címmel rendelkező csomópontra kerül.) Ha a beépített kommunikációs összetevőket használja, ők fogják kezelni a szolgáltatáscímek újrafeloldását.
* Előfordulhat, hogy a szolgáltatás késése átmeneti növekedése, mint a szolgáltatáspéldány elindítja a figyelő újra. Ez attól függ, hogy milyen gyorsan nyitja meg a szolgáltatás a figyelőt a szolgáltatáspéldány áthelyezése után.
* Minden meglévő kapcsolatot le kell zárni, és újra meg kell nyitni, miután a szolgáltatás megnyílik egy új csomóponton. A szabályos csomópontleállítás vagy újraindítás lehetővé teszi a meglévő kapcsolatok szabályos leállítását.

### <a name="test-it-move-service-instances"></a>Tesztelés: Szolgáltatáspéldányok áthelyezése
A Service Fabric tesztképességi eszközeivel tesztforgatókönyvet hozhat, amely különböző módokon teszteli ezeket a helyzeteket:

1. Állapotalapú szolgáltatás elsődleges replikájának áthelyezése.
   
    Az állapotalapú szolgáltatáspartíció elsődleges replikája tetszőleges okok miatt áthelyezhető. Ezzel a cél egy adott partíció elsődleges replikáját, hogy a szolgáltatások reagálnak a lépés egy nagyon ellenőrzött módon.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Csomópont leállítása.
   
    Egy csomópont leállításakor a Service Fabric az adott csomóponton lévő összes szolgáltatáspéldányt vagy partíciót áthelyezi a fürt egy másik elérhető csomópontjára. Ezzel tesztelheti azt a helyzetet, amikor egy csomópont elvész a fürtből, és az adott csomópont összes szolgáltatáspéldányát és replikáját át kell helyeznie.
   
    A PowerShell **Stop-ServiceFabricNode** parancsmag használatával leállíthatja a csomópontot:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>A szolgáltatás elérhetőségének karbantartása
Platformként a Service Fabric úgy lett kialakítva, hogy magas szintű rendelkezésre állást biztosítson a szolgáltatások számára. Szélsőséges esetekben azonban a mögöttes infrastrukturális problémák még mindig elérhetetlenséghez vezethetnek. Fontos, hogy teszteljék ezeket a forgatókönyveket is.

Az állapotalapú szolgáltatások kvórumalapú rendszert használnak az állapot replikálásához a magas rendelkezésre állás érdekében. Ez azt jelenti, hogy a replikák kvórumának rendelkezésre kell állnia az írási műveletek végrehajtásához. Ritka esetekben, például egy széles körű hardverhiba esetén előfordulhat, hogy a replikák kvóruma nem érhető el. Ezekben az esetekben nem lesz képes írási műveleteket végrehajtani, de továbbra is képes lesz olvasási műveleteket végrehajtani.

### <a name="test-it-write-operation-unavailability"></a>Teszt: Írási művelet elérhetetlensége
A Service Fabric tesztelhetőségi eszközeivel olyan hibát adhat be, amely tesztként kvórumvesztést idéz elő. Bár egy ilyen forgatókönyv ritka, fontos, hogy az ügyfelek és a szolgáltatások, amelyek egy állapotalapú szolgáltatás kész kezelni azokat a helyzeteket, amikor nem tudnak írási kérelmeket. Az is fontos, hogy maga az állapotalapú szolgáltatás tisztában legyen ezzel a lehetőséggel, és kecsesen közölhesse azt a hívókkal.

A Kvórum elvesztését a PowerShell **Invoke-ServiceFabricPartitionQuorumLoss** parancsmag használatával idézheti elő:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Ebben a példában `QuorumLossMode` `QuorumReplicas` azt a jelzőt állítottuk be, hogy a kvórum elvesztését az összes replika lebontása nélkül szeretnénk előidézni. Így az olvasási műveletek továbbra is lehetségesek. Ha olyan forgatókönyvet szeretne tesztelni, amelyben egy `AllReplicas`teljes partíció nem érhető el, állítsa ezt a kapcsolót .

## <a name="next-steps"></a>További lépések
[További információ a tesztelhetőségi műveletekről](service-fabric-testability-actions.md)

[További információ a tesztelhetőségi forgatókönyvekről](service-fabric-testability-scenarios.md)

