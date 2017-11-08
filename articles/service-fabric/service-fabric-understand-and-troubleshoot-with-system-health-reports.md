---
title: "Hibaelhárítás a rendszerállapot-jelentések |} Microsoft Docs"
description: "A rendszerállapot-jelentések küldött hibaelhárítási fürt vagy az alkalmazások problémáival Azure Service Fabric-összetevők és használatát ismerteti"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.openlocfilehash: 42dca05c4d7d104ed0e7e21f1e53411e5983cd38
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Rendszerállapot-jelentések használata a hibaelhárítás során
Azure Service Fabric-összetevők adja meg, hogy közvetlenül a kezdő verzióról a fürt összes entitásának a rendszerállapot-jelentéseket. A [a health Store adatbázisban](service-fabric-health-introduction.md#health-store) hoz létre, és törli a rendszer-jelentéseken alapuló entitásokat. Azt is rendszerezi azokat a hierarchiában, amely rögzíti az entitás interakciókat.

> [!NOTE]
> Állapotfigyelő kapcsolatos fogalmak megismerése, olvassa el: [Service Fabric állapotmodell](service-fabric-health-introduction.md).
> 
> 

Rendszerállapot-jelentések adja meg a fürt és az alkalmazás funkciói és jelző problémák láthatósága. Az alkalmazások és szolgáltatások rendszerállapot-jelentések győződjön meg arról, hogy az entitások vannak megvalósítva, és a Service Fabric szempontjából megfelelően működik. A jelentések nem biztosítanak semmilyen állapotfigyelés az üzleti logika, a szolgáltatás vagy a lefagyott folyamatok észlelése. Felhasználó szolgáltatások állapotfigyelő üzemállapot-adatait a logika vonatkozó információkkal.

> [!NOTE]
> Felhasználói watchdogs által küldött állapotjelentések láthatók csak *után* rendszerösszetevőn entitás létrehozása. A törölt egy entitás a health Store adatbázisban automatikusan törli a vele társított összes rendszerállapot-jelentések. Ugyanez igaz. Ha az entitás egy új példánya jön létre, például, amikor egy új állapotfüggő szolgáltatáspéldány replika jön létre. A régi példányhoz társított összes jelentések törlése, és a-tárolóból törölni.
> 
> 

A rendszer az összetevő jelent azonosítják a forrás, amely kezdődik-e a "**System.**" előtag. Watchdogs nem használhatja ugyanazt az előtagot az eseményforrásokat, érvénytelen paramétereket tartalmazó jelentések utasítja el.

Vizsgáljuk meg néhány rendszer jelentések segítségével megtudhatja, mi váltja ki őket, és megtudhatja, hogyan javítsa ki a lehetséges problémák jelölnek.

> [!NOTE]
> A Service Fabric tovább jelentések hozzáadása a feltételek egyik fontos, hogy mi történik a fürt láthatósága javítása, és az alkalmazások meglévő jelentések fejleszthető a további részleteket a probléma gyorsabb megoldása érdekében.
> 
> 

## <a name="cluster-system-health-reports"></a>Fürt rendszerállapot-jelentések
A fürt állapotának entitás a health Store adatbázisban automatikusan jön létre. Ha minden megfelelően működik, a rendszer a jelentés nem tartalmaz.

### <a name="neighborhood-loss"></a>Hálózatok adatvesztés
**System.Federation** egy hibát jelez, amikor azt észleli, hogy a helyek adatvesztés. A jelentés az egyes csomópontokon, és a csomópont-azonosító szerepel a tulajdonságnév. Ha egy alkalmazás a teljes Service Fabric gyűrű elvesznek, általában számíthat két olyan események, amelyek megfelelnek a résnek jelentés mindkét oldalán. További környékeken elvesznek, ha nincsenek további események.

A jelentés a globális bérleti idejét adja meg az idő-live (TTL). A jelentés minden fele a TTL időtartama Újraküldte mindaddig, amíg a feltétel aktív marad. Az esemény lejáratot követően automatikusan törlődnek. Távolítsa el a Ha-lejárt viselkedés garantálja, hogy, hogy a jelentés törlődnek a a health Store adatbázisból megfelelően, még akkor is, ha a jelentéskészítési csomópont nem működik.

* **SourceId**: System.Federation
* **Tulajdonság**: kezdődik **hálózatok** és csomópont információkat tartalmaz.
* **További lépések**: vizsgálja meg, miért a helyek elvész, például, a fürt csomópontjai közötti kommunikáció.

### <a name="rebuild"></a>Építse újra

A **Feladatátvevőfürt-kezelő** szolgáltatás (**FM**) a fürt csomópontjai kapcsolatos információt. Amikor FM elveszíti az adatokat, és azt nem garantálja az adatvesztéssel hiányzóra rendelkezik a fürtcsomópontokon a legfrissebb információkat. Ebben az esetben a rendszer végighalad egy **újraépítése**, és **System.FM** adatokat gyűjt a fürt összes csomópontján ahhoz, hogy építse újra az állapotát. Egyes esetekben hálózati vagy a csomópont problémák miatt rebuild sikerült akadt-e vagy leállt. Azonos fordulhat elő a **Feladatátvevőfürt-kezelő Master** szolgáltatás (**FMM**). A **FMM** állapotmentes rendszer szolgáltatás, amely nyomon követi a where összes a **FMs** vannak a fürtben. A **FMMs** elsődleges van mindig 0 legközelebbi azonosítójú csomópont. Ha a csomóponton eldobott kapja, egy **építse újra** elindul.
Az előző feltételek valamelyike esetén fordulhat elő, amikor **System.FM** vagy **System.FMM** megjelölés keresztül egy esetleges hibajelentésben való megjelenítéshez. Újraépítés elakadt egy két lépésből áll:

* Várakozás a szórás: **FM/FMM** megvárja-e a többi csomópontjáról a szórási üzenet érkezett válasz. **További lépések:** vizsgálja meg, hogy van-e a hálózati kapcsolati hibáája csomópontok között.   
* Várakozás a csomópontok: **FM/FMM** már szórási válasz érkezett a többi csomópont, és arra vár, hogy egy adott csomópont válasza. A jelentés felsorolja a csomópont, amelynek a **FM/FMM** választ vár. **További lépések:** vizsgálja meg a hálózati kapcsolatot a **FM/FMM** , valamint a felsorolt csomópontot. Minden felsorolt csomópont egyéb lehetséges problémákat vizsgálja meg.

* **SourceID**: System.FM vagy System.FMM
* **Tulajdonság**: építse újra.
* **További lépések**: vizsgálja meg a hálózati kapcsolat a csomópontok, valamint a szerinti táblázat felsorolja a jelentés leírását a megadott csomópontok között.

## <a name="node-system-health-reports"></a>Csomópont rendszerállapot-jelentések
**System.FM**, a szolgáltatót, amely a fürtcsomópontokon kapcsolatos információt, amely jelenti, hogy a Feladatátvevőfürt-kezelő szolgáltatás. Minden csomópont állapotát megjelenítő System.FM egy jelentést kell rendelkeznie. A csomópont entitásokat a rendszer törli, a csomópont állapota eltávolításakor. További információkért lásd: [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>A csomópont fel/le
Amikor a csomópont csatlakozik a (is működik, és) gyűrű a OK System.FM jelentés. Az egy hibát jelez, amikor a csomópont a gyűrűben indulását (szolgáltatás leállt, vagy frissítés, vagy egyszerűen mert sikertelen volt). A rendszerállapot-hierarchia által a health Store adatbázisban végzett System.FM csomópontokhoz kapcsolódó jelentések korrelációban állnak a telepített entitás. Úgy ítéli meg, a csomópont minden telepített entitás virtuális szülője. Ezen a csomóponton telepített entitások közzétéve az lekérdezésekben, ha a csomópont állapotúként jelentette-e System.FM entitások társított példányt az azonos példánnyal rendelkező által. Amikor System.FM jelzi, hogy a csomópont nem működik, vagy újraindul, egy új példányt a health Store adatbázisban automatikusan a szükségtelenné vált a telepített entitás, amely csak a lefelé csomópontján vagy a csomópont előző példány létezhet.

* **SourceId**: System.FM
* **Tulajdonság**: állapot.
* **További lépések**: Ha a csomópont nem működik a frissítéshez, azt kell helyreállnak van a frissítése után. Ebben az esetben állapotát kell váltson vissza az OK gombra. A csomópont nem térjen vissza, vagy nem sikerül, ha a probléma további vizsgálat van szüksége.

A következő példa bemutatja a csomópont OK állapotot System.FM esemény:

```PowerShell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Tanúsítvány lejárta
**System.FabricNode** jelentések figyelmeztetés, ha a csomópont által használt tanúsítványok lejáró. Három tanúsítvány csomópontonként: **Certificate_cluster**, **Certificate_server**, és **Certificate_default_client**. Legalább két héttel a lejárati esetén a jelentés állapota rendben. A lejárati két héten belül van, a jelentés típusa esetén figyelmeztetés. Ezek az események TTL végtelen, és amikor a csomópont elhagyja a fürt eltávolítja.

* **SourceId**: System.FabricNode
* **Tulajdonság**: kezdődik **tanúsítvány** és a tanúsítvány típusát további információt tartalmaz.
* **További lépések**: frissítse a tanúsítványokat, ha azok lejáró.

### <a name="load-capacity-violation"></a>Betöltési kapacitás megsértése
A Service Fabric terheléselosztó hiányára amikor azt észleli, hogy a csomópont-kapacitás megsértése.

* **SourceId**: System.PLB
* **Tulajdonság**: kezdődik **kapacitás**.
* **További lépések**: a megadott metrikák és olvassa el az aktuális kapacitása a csomóponton.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Csomópont kapacitás eltérés erőforrás irányítás metrikáihoz
Figyelmeztetést, hogy a csomópont-kapacitás meghatározása a fürtjegyzékben System.Hosting jelentések nagyobbak, mint a valódi csomópont kapacitások erőforrás irányítás metrikák (memória- és magok). Állapotjelentés jelennek első szolgáltatáscsomagot használó [erőforrás irányítás](service-fabric-resource-governance.md) regisztrál egy adott csomóponton.

* **SourceId**: System.Hosting
* **Tulajdonság**: ResourceGovernance
* **További lépések**: Ez problémát okozhat a vonatkozó szolgáltatás-csomagokat a rendszer nem érvényesíti várt módon, és [erőforrás irányítás](service-fabric-resource-governance.md) nem fog megfelelően működni. A fürtjegyzékben frissíteni ezeket a mérési megfelelő csomópont kapacitások vagy nem megadnia őket minden és lehetővé teszik a Service Fabric automatikusan felismeri a rendelkezésre álló erőforrások.

## <a name="application-system-health-reports"></a>Alkalmazás rendszerállapot-jelentések
**System.CM**, amely jelenti, hogy a kezelő szolgáltatás a szolgáltató, amely felügyeli az alkalmazással kapcsolatos információkat.

### <a name="state"></a>Állapot
System.CM jelzi az OK gombra az alkalmazás létrehozásakor vagy frissítésekor. Az tájékoztatja a health Store adatbázisban az alkalmazás törlésekor, hogy el kell távolítani az áruházból.

* **SourceId**: System.CM
* **Tulajdonság**: állapot.
* **További lépések**: Ha az alkalmazás létrehozott vagy frissített, a kezelő állapotjelentése tartalmaznia kell. Ellenkező esetben ellenőrizze az alkalmazás állapota egy lekérdezést, például a PowerShell-parancsmag kiállításával **Get-ServiceFabricApplication - ApplicationName** *applicationName*.

Az alábbi példában látható az esemény a **fabric: / WordCount** alkalmazás:

```PowerShell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Szolgáltatás rendszerállapot-jelentések
**System.FM**, amely jelenti, hogy a Feladatátvevőfürt-kezelő szolgáltatás, amely kezeli a szolgáltatással kapcsolatban további információt a szolgáltató.

### <a name="state"></a>Állapot
System.FM jelzi az OK gombra a szolgáltatás létrehozásakor. Ez törli az entitás a health Store adatbázisban a szolgáltatás törlésekor.

* **SourceId**: System.FM
* **Tulajdonság**: állapot.

A következő példa bemutatja az esemény a szolgáltatás **fabric: / WordCount/wordcountwebservice verziója**:

```PowerShell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Korrelációs hibája
**System.PLB** egy hibát jelez, amikor azt észleli, hogy egy másik szolgáltatás, amely létrehoz egy affinitási lánc szolgáltatás frissítése tartozzanak. A jelentés egy sikeres frissítés történik, ha nincs bejelölve.

* **SourceId**: System.PLB
* **Tulajdonság**: ServiceDescription leírásban.
* **További lépések**: Ellenőrizze a kapcsolódó szolgáltatások ismertetése.

## <a name="partition-system-health-reports"></a>Partíció rendszerállapot-jelentések
**System.FM**, a szolgáltatót, amely a szolgáltatáspartíciók kapcsolatos információt, amely jelenti, hogy a Feladatátvevőfürt-kezelő szolgáltatás.

### <a name="state"></a>Állapot
System.FM jelzi az OK gombra, ha a partíció létrejött, és megfelelő állapotban. Ez törli az entitás a health Store adatbázisban a partíció törlése.

A partíció nem éri el a replikakészlet minimális száma, ha hiba jelenti. Ha a partíció nincs kevesebb a minimális replika, de kevesebb a cél replika, a rendszer figyelmeztetést jelenti. Ha a partíció kvórumveszteségben van, System.FM jelent hibát.

Egyéb figyelmet a jelentősebb események tartalmazhat figyelmeztetés, ha az újrakonfigurálás hosszabb időt vesz igénybe a vártnál, és amikor a vártnál hosszabb ideig tart a build. A várt alkalommal a build és újrakonfigurálását konfigurálható a következők alapján a szolgáltatás forgatókönyveket. Például egy terabájt állapot, például az Azure SQL Database, a szolgáltatás-e a build tovább tart, mint egy szolgáltatás állapotát kis mennyiségű számára.

* **SourceId**: System.FM
* **Tulajdonság**: állapot.
* **További lépések**: Ha a rendszerállapot nem OK, úgy is, hogy egyes másodpéldányok nem létrehozott, megnyitott, vagy a tartományvezérlővé való előléptetés elsődleges vagy másodlagos megfelelően. 

A leírás a kvórum elvesztése írja le, majd a replikákról, nem működnek a részletes állapotjelentése vizsgálata és biztonsági mentése segít a partíció érdekében állapotba hozza őket biztonsági online.

Ha a leírás Beragadt partíció ismerteti [újrakonfigurálás](service-fabric-concepts-reconfiguration.md), majd a jelentés az elsődleges replikán nyújt további információkat.

Az egyéb System.FM állapotjelentések nem lenne a replikákat vagy a partíció vagy az egyéb rendszerösszetevők szolgáltatás jelentéseket. 

Az alábbi példák némelyike ezekre a jelentésekre ismertetik. 

A következő példa bemutatja a megfelelő partíció:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

A következő példa bemutatja, javasoljuk, hogy kevesebb cél replika állapotát. A következő lépés az, hogy beolvasása a partíció leírása, amely bemutatja, hogyan van konfigurálva: **MinReplicaSetSize** három és **TargetReplicaSetSize** hét. Majd kérje le a csomópontok száma a fürtben, amely ebben az esetben az öt. Igen ebben az esetben két replika nem helyezhető, mert a cél replikák száma nagyobb, mint az elérhető csomópontok számát.

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

A következő példa bemutatja javasoljuk, hogy Beragadt állapotát az újrakonfigurálás a felhasználó nem érvényesítenie megszakítását miatt a jogkivonat a **RunAsync** metódust. Vizsgálja meg a jelentés bármely megjelölt az elsődleges replika (P) segítségével részletezést lefelé további a problémát.

```PowerShell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
A jelentés megjeleníti az állapotát a replikák a partíció újrakonfigurálás alatt áll: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Minden egyes replikához az állapotjelentést tartalmazza:
- Előző konfigurációs szerepkör
- A jelenlegi konfiguráció szerepkörnek
- [A replika állapota](service-fabric-concepts-replica-lifecycle.md)
- Csomópont, amelyen a replika fut.
- Másodpéldány-azonosító:

A példához hasonlóan esetben további vizsgálat szükséges. Minden egyes replikát kezdődő, és a replikák jelölésű állapotának vizsgálata `Primary` és `Secondary` (131482789658160654 és 131482789688598467) az előző példában.

### <a name="replica-constraint-violation"></a>A replika korlátozássértést
**System.PLB** jelentések figyelmeztetés, ha egy replika korlátozássértést észlelt, és nem helyezhető el az összes partíció replika. A jelentés-részletek megjelenítése milyen korlátozások és tulajdonságok megakadályozza a replika elhelyezésének.

* **SourceId**: System.PLB
* **Tulajdonság**: kezdődik **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>A replika rendszerállapot-jelentések
**System.RA**, amely jelenti, hogy a reconfiguration agent összetevő a replika állapota az szolgáltatót.

### <a name="state"></a>Állapot
System.RA jelentések OK, a replika létrehozása után.

* **SourceId**: System.RA
* **Tulajdonság**: állapot.

A következő példa bemutatja a megfelelő replika:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Ez a tulajdonság segítségével jelzik a figyelmeztetések vagy hibák, amikor megpróbálta megnyitni egy replikát, zárja be a replika, vagy szüntesse meg a replika egy szerepkört a másikra. További információkért lásd: [replika életciklus](service-fabric-concepts-replica-lifecycle.md). A hibák az API-hívások vagy a ebben az időszakban a szolgáltatás gazdafolyamat összeomlások kiváltott kivételekre is ki lehet. A hiba oka, hogy az API-hívásokban a C#-kódban a Service Fabric ad hozzá a kivétel és Veremkivonat az állapotjelentést.

Állapotfigyelő figyelmeztetések jelenik meg, a művelet helyileg bizonyos számú alkalommal (attól függően, hogy a házirend) újrapróbálkozás után. A Service Fabric újrapróbálkozik a művelet legfeljebb egy maximális határérték. A maximális küszöb elérése után, próbáljon a hierarchiakezelő elhárította való. Ez a kísérlet beolvasása törölve ezen a csomóponton műveletei nyújtja figyelmeztetések okozhat. Például ha egy replikát nyisson meg egy csomóponton nem sikerült, a Service Fabric egy állapotfigyelési figyelmeztetése riasztást. Ha továbbra is a replika megnyitása sikertelen, a Service Fabric önálló kijavításához működik. Ez a művelet vonatkozhat a azonos kísérlete egy másik csomópontjára. Ennek hatására a figyelmeztető üzenet jelenik meg a törlendő replikára vonatkozóan. 

* **SourceId**: System.RA
* **Tulajdonság**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, és **ReplicaChangeRoleStatus**.
* **További lépések**: vizsgálja meg a szolgáltatást kód vagy összeomlási memóriaképek azonosítására, ezért a művelet sikertelen.

A következő példa bemutatja szűrész replika állapotának `TargetInvocationException` a nyitott metódusból. A leírás tartalmazza a pontok kialakulását, **IStatefulServiceReplica.Open**, a kivétel típusát **TargetInvocationException**, és az alábbi veremkivonatban.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

A következő példa bemutatja a Bezárás során folyamatosan összeomló replika:

```PowerShell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Újrakonfigurálása
Ezzel a tulajdonsággal lejátszásához, ha egy replika hajt végre egy [újrakonfigurálás](service-fabric-concepts-reconfiguration.md) észleli, hogy az újrakonfigurálás leállt vagy akadt-e. A jelentés a replikán, amelynek jelenlegi szerepköre: elsődleges, kivéve a lapozófájl-kapacitás elsődleges újrakonfigurálás esetekben előfordulhat, ha a replikán, amely elsődleges aktív másodlagos lefokozni lehet.

Az újrakonfigurálás is elakadt a következő okok valamelyike:

- Egy műveletet a helyi replika, megegyezik a végrehajtása az újrakonfigurálás azonos replika nem befejeződik. Ebben az esetben vizsgálja a rendszerállapot-jelentések más összetevői a replikán, System.RAP, illetve System.RE, előfordulhat, hogy további információkkal szolgálnak.

- Egy műveletet nem befejeződik a távoli replikáját. Az állapotjelentés, amelynek műveletek várakoznak replikák láthatók. További vizsgálat a rendszerállapot-jelentések e távoli replikákra vonatkozó kell elvégezni. Előfordulhat, hogy is van a csomópont és a távoli csomóponton közötti kommunikációs problémák.

Ritka esetekben újrakonfigurálását is elakadt a kommunikáció vagy egyéb problémák, a csomópont és a Feladatátvevőfürt-kezelő szolgáltatás között.

* **SourceId**: System.RA
* **Tulajdonság**: **újrakonfigurálás**.
* **További lépések**: vizsgálja meg a helyi vagy távoli replikák attól függően, hogy a jelentés leírását.

A következő példa bemutatja egy jelentés, ha egy újrakonfigurálás a helyi replika akadt-e. Ez a példa azt van miatt a szolgáltatás nem érvényesítenie a megszakítási jogkivonat.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

Az alábbi példában látható, a health jelentést, ahol egy újrakonfigurálás Beragadt két távoli replika válaszára várakozik. Ebben a példában nincsenek három replikák a partíció, beleértve az aktuális elsődleges. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

A jelentés jeleníti meg, hogy az újrakonfigurálás Beragadt két replika válaszára Várakozás: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Minden egyes replikához adja a következő információkat:
- Előző konfigurációs szerepkör
- A jelenlegi konfiguráció szerepkörnek
- [A replika állapota](service-fabric-concepts-replica-lifecycle.md)
- Csomópont-azonosító
- Másodpéldány-azonosító:

Az újrakonfigurálás feloldása:
- A **le** replikák kell foglalni. 
- A **található inbuild** replikák kell végrehajtani a build és készen állnak.

### <a name="slow-service-api-call"></a>Lassú service API-hívás
**System.RAP** és **System.Replicator** jelentést egy figyelmeztetés, ha a szolgáltatás felhasználói kód hívása tovább tart, mint a beállított időn. A figyelmeztetés nincs bejelölve, a hívás befejezését.

* **SourceId**: System.RAP vagy System.Replicator
* **Tulajdonság**: a lassú API neve. A leírás nyújt további információt az idő, az API-t a függőben lévő lett.
* **További lépések**: vizsgálja meg, miért vártnál hosszabb ideig tart a hívást.

A következő példa bemutatja a állapotesemény System.RAP a megbízható szolgáltatás, amely nem van érvényesítenie ezzel a token **RunAsync**:

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

A tulajdonság és a szöveg jelzi, mely API kapott akadt-e. A következő lépésekkel különböző lefagyott API-k nem egyezik. Az API-k a *IStatefulServiceReplica* vagy *IStatelessServiceInstance* általában programhiba a szolgáltatás kódban van. Az alábbi szakasz ismerteti, hogyan ezek lefordítani a [Reliable Services modell](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: Ez a figyelmeztetés azt jelzi, hogy hívása `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, vagy felül, ha `OnOpenAsync` Beragadt.

- **IStatefulServiceReplica.Close** és **IStatefulServiceReplica.Abort**: A leggyakoribb eset egy olyan szolgáltatás, a továbbított cancellation jogkivonat nem érvényesítenie `RunAsync`. Azt is lehet, hogy `ICommunicationListener.CloseAsync`, vagy felül, ha `OnCloseAsync` Beragadt.

- **IStatefulServiceReplica.ChangeRole (S)** és **IStatefulServiceReplica.ChangeRole(N)**: A leggyakoribb eset egy olyan szolgáltatás, a továbbított cancellation jogkivonat nem érvényesítenie `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: A leggyakoribb eset az, hogy a szolgáltatás nem adott vissza egy feladat `RunAsync`.

A rendszer más is elakadnak API-hívások a **IReplicator** felületet. Példa:

- **IReplicator.CatchupReplicaSet**: Ez a figyelmeztetés azt jelzi, két lehetőség közül. Vagy nem elegendők mentése replikákat, amely alapján a replikákat a partíció vagy az System.FM állapotjelentés lefagyott újrakonfigurálása a replika állapota lehet meghatározni. Vagy a replikák nem vannak igazolása műveletek. A PowerShell-parancs-let `Get-ServiceFabricDeployedReplicaDetail` a replikák állapotának meghatározásához használható. A probléma van replikák alkalmazáscsoportokból `LastAppliedReplicationSequenceNumber` mögötti az elsődleges `CommittedSequenceNumber`.

- **IReplicator.BuildReplica (<Remote ReplicaId>)**: Ez a figyelmeztetés az összeállítási folyamat kapcsolatos problémát jelez. További információkért lásd: [replika életciklus](service-fabric-concepts-replica-lifecycle.md). Előfordulhat, hogy a replikációs cím egy helytelen konfiguráció miatt. További információkért lásd: [állapotalapú Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md) és [adja meg az erőforrásokat. a szolgáltatás jegyzékben](service-fabric-service-manifest-resources.md). A távoli csomóponton levő probléma is lehet.

### <a name="replication-queue-full"></a>Replikációs sor megtelt
**System.Replicator** jelentések figyelmeztetés, ha a replikációs sor megtelt. Az elsődleges a replikációs várólistában általában megtelik, mert egy vagy több másodlagos replikák lassúak múlva nyugtázza a műveletek. A másodlagos Ez általában történik, ha a szolgáltatás lassú műveletek alkalmazásához. A figyelmeztetés nincs bejelölve, ha a várólista már nem teljes.

* **SourceId**: System.Replicator
* **Tulajdonság**: **PrimaryReplicationQueueStatus** vagy **SecondaryReplicationQueueStatus**, attól függően, a replika szerepkör.

### <a name="slow-naming-operations"></a>Lassú elnevezési műveletek
**System.NamingService** állapotát jelenti az elsődleges replikán, amikor egy elnevezési művelet elfogadható hosszabb időbe telik. Példák elnevezési műveletek [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) vagy [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Több metódust alatt található FabricClient, például a [módszereket szolgáltatás](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) vagy [tulajdonság módszereket](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> A Naming service szolgáltatásnevek megszünteti a fürt helyre, és lehetővé teszi a felhasználók kezeléséhez a szolgáltatás nevét és tulajdonságait. A Service Fabric particionálva megőrzött szolgáltatást is. A partíciók közül a *Authority Owner*, amely tartalmazza az összes Service Fabric-neveket és szolgáltatásokra vonatkozó metaadatok. A Service Fabric-nevek van leképezve a különböző partíciók nevű *Name Owner* partíciók, így a szolgáltatás nem bővíthető. További információ a [Naming service](service-fabric-architecture.md).
> 
> 

Egy elnevezési művelet a vártnál hosszabb időt vesz igénybe, ha a művelet a Naming service partíció a művelet látja, hogy az elsődleges replikán figyelmeztetés jelentést van megjelölve. Ha a művelet sikeresen befejeződött, a figyelmeztetés nincs bejelölve. Ha a művelet egy hibával befejeződött, a jelentés tartalmazza a hiba részleteit.

* **SourceId**: System.NamingService
* **Tulajdonság**: előtaggal kezdődik "**Duration_**", és azonosítja a lassú művelet és a Service Fabric nevét, amelyen a művelet történik. Például ha a szolgáltatás létrehozása a következő néven **fabric: / MyApp/MyService** túl sokáig tart, a tulajdonság értéke **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" szerepe a Naming ezt a nevet és a művelet a mutat.
* **További lépések**: Ellenőrizze, hogy miért a Naming művelet sikertelen lesz. Minden műveletet lehet különböző alapvető okait. Például a törlés szolgáltatás Beragadt. A szolgáltatás elakadt, mert alkalmazásgazda tartja összeomló szolgáltatáskód felhasználói hibája miatt a csomóponton.

A következő példa bemutatja a szolgáltatás létrehozási művelet. A művelet a megadott időtartamnál hosszabb ideig tartott. "AO" újrapróbálkozik, és munkahelyi küld a "nem". "Nem" befejezni az utolsó művelet – időtúllépés miatt. Ebben az esetben a azonos replika nem elsődleges, mind a "AO", pedig a "Nem" szerepkör.

```PowerShell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication rendszerállapot-jelentések
**System.Hosting** a szervezet az üzembe helyezett entitásokra.

### <a name="activation"></a>az aktiválás
System.Hosting jelzi az OK gombra, ha egy alkalmazás aktiválása megtörtént a csomóponton. Ellenkező esetben azt egy hibát jelez.

* **SourceId**: System.Hosting
* **Tulajdonság**: aktiválási, beleértve a bevezetés verzióját.
* **További lépések**: Ha az alkalmazás állapota nem megfelelő, vizsgálja meg, miért nem sikerült az aktiválást.

A következő példa bemutatja a sikeres aktiválási:

```PowerShell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Letöltés
System.Hosting egy hibát jelez, ha az alkalmazás csomag letöltése sikertelen.

* **SourceId**: System.Hosting
* **Tulajdonság**: **letöltése:***RolloutVersion*.
* **További lépések**: vizsgálja meg, miért nem sikerült a letöltés a csomóponton.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage rendszerállapot-jelentések
**System.Hosting** a szervezet az üzembe helyezett entitásokra.

### <a name="service-package-activation"></a>Szolgáltatás az alkalmazáscsomag aktiválása
System.Hosting jelentések OK gombra, ha a csomóponton a szolgáltatás az alkalmazáscsomag aktiválása sikeres. Ellenkező esetben azt egy hibát jelez.

* **SourceId**: System.Hosting
* **Tulajdonság**: aktiválás.
* **További lépések**: vizsgálja meg, miért nem sikerült az aktiválást.

### <a name="code-package-activation"></a>Kód az alkalmazáscsomag aktiválása
System.Hosting jelentések OK kód csomagonként Ha az aktiválás sikeres. Ha az aktiválás sikertelen, akkor hiányára konfigurált módon. Ha **CodePackage** nem tudja aktiválni vagy nagyobb, mint a beállított hibával leáll **CodePackageHealthErrorThreshold**, üzemeltető egy hibát jelez. A service-csomag kód több csomagot tartalmaz, az aktiválási jelentés minden egyes jön létre.

* **SourceId**: System.Hosting
* **Tulajdonság**: az előtag- **CodePackageActivation** és nevét, valamint a kódcsomag a belépési pontot tartalmaz **CodePackageActivation:***CodePackageName* :*SetupEntryPoint/EntryPoint*. Például **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Szolgáltatási típus regisztrációs
System.Hosting OK jelent, ha a szolgáltatás típusának regisztrálása sikeresen befejeződött. Egy hibát jelez, ha a regisztrációját nem végezték el időben, használatával konfigurált **ServiceTypeRegistrationTimeout**. A futtatókörnyezet le van zárva, ha a szolgáltatás típusa a csomópont regisztrációját, és üzemeltetési hiányára.

* **SourceId**: System.Hosting
* **Tulajdonság**: az előtag- **ServiceTypeRegistration** és a szolgáltatás környezettípus nevét tartalmazza. Például **ServiceTypeRegistration:FileStoreServiceType**.

A következő példa bemutatja a megfelelő telepített szervizcsomag:

```PowerShell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Letöltés
Ha a service-csomag letöltése sikertelen, System.Hosting jelent hibát.

* **SourceId**: System.Hosting
* **Tulajdonság**: **letöltése:***RolloutVersion*.
* **További lépések**: vizsgálja meg, miért nem sikerült a letöltés a csomóponton.

### <a name="upgrade-validation"></a>Frissítésének ellenőrzése
System.Hosting egy hibát jelez, ha a frissítés során az érvényesítés meghiúsul, vagy ha a frissítés sikertelen lesz a csomóponton.

* **SourceId**: System.Hosting
* **Tulajdonság**: az előtag- **FabricUpgradeValidation** , és tartalmazza a frissített verzió.
* **Leírás**: a következő hiba mutat.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Nem definiált csomópont-kapacitás erőforrás irányítás metrikáihoz
System.Hosting jelentések figyelmeztetés, ha a csomópont-kapacitás nem definiált a fürtjegyzékben, és az automatikus észleléshez config ki van kapcsolva. A Service Fabric emeli állapotfigyelési figyelmeztetése, amikor a szolgáltatáscsomagot használó [erőforrás irányítás](service-fabric-resource-governance.md) regisztrál egy adott csomóponton.

* **SourceId**: System.Hosting
* **Tulajdonság**: ResourceGovernance
* **További lépések**: Ez a probléma megoldásához, előnyben részesített módja a módosítása a fürtjegyzékben rendelkezésre álló erőforrások automatikus észleléséhez. Másik módja a fürtjegyzékben frissíti a-es helytelenül megadott csomópont a következő metrikák tekintetében.

## <a name="next-steps"></a>Következő lépések
[A Service Fabric rendszerállapot-jelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

[Jelentés és a szolgáltatás állapotának ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Figyelése és diagnosztizálása helyileg szolgáltatások](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

