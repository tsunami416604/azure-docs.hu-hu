---
title: Hibaelhárítás rendszerállapot-jelentések segítségével |} A Microsoft Docs
description: A rendszerállapot-jelentések hibaelhárítási fürt vagy alkalmazásokkal kapcsolatos problémák az Azure Service Fabric-összetevők és a használatuk által küldött ismerteti
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d9b3ba8d216f3e82c9aff7f2b49b9c24115b32f2
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487906"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Rendszerállapot-jelentések használata a hibaelhárítás során
Azure Service Fabric-összetevőket meg rendszerállapot-jelentéseket a lehetőségeiből közvetlenül a fürtön lévő összes entitáshoz. A [health Store adatbázisban](service-fabric-health-introduction.md#health-store) hoz létre, és törli az entitásokat a rendszer jelentések alapján. Azt is rendezi őket a hierarchiában, amely entitás interakciót rögzíti.

> [!NOTE]
> Szeretné megtudni, egészségügyi kapcsolatos fogalmakat, további információk: [Service Fabric állapotmodell](service-fabric-health-introduction.md).
> 
> 

Rendszerállapot-jelentések betekintést fürt és az alkalmazás funkciói, és problémákat jelző. Alkalmazások és szolgáltatások rendszerállapot-jelentések győződjön meg arról, hogy az entitások vannak megvalósítva, és megfelelően a Service Fabric-perspektívát vezérelhesse. A jelentéseket bármely szolgáltatásállapot-figyelést az üzleti logika, a szolgáltatás vagy a lefagyott folyamatok észlelése nem ad meg. Olyan szolgáltatást gazdagabbá teheti az egészségügyi adatok saját logika információkkal.

> [!NOTE]
> Felhasználói watchdogs által küldött rendszerállapot-jelentések láthatók csak *után* a rendszer összetevőit az entitás létrehozását. Egy entitás törlésekor a health Store adatbázisban automatikusan törli a hozzá társított összes rendszerállapot-jelentések. Ugyanez igaz, amikor az entitást egy új példánya jön. Például, ha egy új állapot-nyilvántartó megőrzött replika szolgáltatáspéldány jön létre. A régi példányhoz társított összes jelentés törölve, és a tároló törlődik.
> 
> 

A rendszer az összetevő jelent, hogy a forrás, amely kezdődik azonosítja a "**System.**" prefix. Watchdogs nem használhatja ugyanazon előtaggal eseményforrásokat, a jelentések érvénytelen paraméterekkel a rendszer elutasítja.

Tekintsünk meg néhány rendszer jelentések segítségével megtudhatja, milyen elindítja őket, és megtudhatja, hogyan képviselik a potenciális problémák kijavításához.

> [!NOTE]
> A Service Fabric-jelentéseket vehetnek fel a feltételek a lényeges, hogy mi történik a fürt és az alkalmazások láthatósága javítása továbbra is. Meglévő jelentések fejleszthető a további részleteket a probléma gyorsabb hibaelhárítás elősegítése érdekében.
> 
> 

## <a name="cluster-system-health-reports"></a>Fürt rendszerállapot-jelentések
A fürt állapotának entitás automatikusan létrejön a health Store adatbázisban. Ha minden megfelelően működik, akkor a rendszer a jelentés nincs.

### <a name="neighborhood-loss"></a>Szomszédságvesztés
**System.Federation** hibát jelez, ha azt észleli, hogy egy szomszédságvesztés. A jelentés egyes csomópontjairól más csomópontokra, és a csomópont-azonosító szerepel a tulajdonság nevét. Ha egy alkalmazás a Service Fabric teljes gyűrű elnyomja, általában várható két események, amelyek a gap jelentés mindkét oldalán. További környékeken elvesznek, ha nincsenek további események.

A jelentés a globális bérleti idejét az idő-az-élettartam (TTL) határozza meg. A jelentés minden a TTL időtartam felét Újraküldte mindaddig, amíg a feltétel aktív marad. A lejárat után a rendszer automatikusan eltávolítja az eseményt. Remove-Ha lejárt a viselkedés garantálja, hogy, hogy a jelentés törlődnek a health store-ból, még akkor is, ha a jelentéskészítési csomópont nem működik.

* **SourceId**: System.Federation
* **Vlastnost**: Kezdődik **hálózatok** és csomópontjának adatait tartalmazza.
* **További lépések**: Vizsgálja meg, miért érdemes a hálózatok elvész. Például ellenőrizze a fürt csomópontok közötti kommunikációt.

### <a name="rebuild"></a>Újraépítés

A feladatátvételi Manager (FM) szolgáltatás kezeli a fürtcsomópontok kapcsolatos információkat. FM elveszíti az adatokat és lépnek az adatvesztést, ha nem garantálja, hogy rendelkezik-e a fürt csomópontjai a legtöbb naprakész információt. Ebben az esetben a rendszer végighalad az újjáépítést, és System.FM adatokat gyűjt a fürt összes csomópontja annak érdekében, hogy építse újra az állapotában. Egyes esetekben hálózati vagy a csomópont problémák miatt Újraépítés is elakadt vagy leállt. Ugyanaz, akkor fordulhat elő, a feladatátvétel kezelő főkiszolgáló (FMM) szolgáltatással. Az FMM állapotmentes rendszer szolgáltatása követi nyomon a fürt összes FMs amelyeknél. Az FMM elsődleges, mindig 0 legközelebbi azonosítójú csomópont. Ha lekérdezi a csomóponton, akkor aktiválódik, újjáépítést.
Ha az előző feltételek egyike történik, **System.FM** vagy **System.FMM** hibajelentés keresztül megőrzendő tartalomként jelöli. Újraépítés Beragadt egy két fázisban történik:

* **Várakozás a közvetítés**: Az FM/FMM megvárja, amíg a többi csomópont szórásos üzenet válaszára.

  * **További lépések**: Vizsgálja meg, hogy van-e olyan hálózati kapcsolati hibáája csomópontok között.
* **Várakozás a csomópontokra**: Az FM/FMM már a többi csomópont szórásos választ kapott, és csomópontok válaszára várakozik. Az állapotjelentés sorolja fel, amelynek a FM/FMM választ vár a csomópontokat.
   * **További lépések**: Vizsgálja meg a hálózati kapcsolat a FM/FMM és a listán szereplő csomópontok között. Vizsgálja meg az egyes felsorolt csomópont egyéb lehetséges problémákat.

* **SourceID**: System.FM vagy System.FMM
* **Vlastnost**: Építse újra.
* **További lépések**: Vizsgálja meg a csomópontok, valamint bármely adott azért az egészségügyi jelentés leírása a felsorolt közötti hálózati kapcsolat.

## <a name="node-system-health-reports"></a>Csomópont rendszerállapot-jelentések
System.FM jelöli meg a Feladatátvevőfürt-kezelő szolgáltatás, amely a szolgáltató, amely kezeli a fürtcsomópontok információ. Minden egyes csomópont állapotát megjelenítő System.FM egy jelentést kell rendelkeznie. A csomópont entitások el lesznek távolítva, amikor a rendszer eltávolítja a csomópont állapota. További információkért lásd: [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>Csomópont felfelé és lefelé
System.FM jelentések OK, amikor a csomópont csatlakozik a gyűrű (azt a már működő). Ez hibát jelez, amikor a csomópont a kör indulását (szolgáltatás leállt, vagy frissítéséhez szükséges vagy egyszerűen, mert sikertelen volt). A health-hierarchia a health Store adatbázisban által készített végzett üzembe helyezett entitások korrelációs System.FM csomópont jelentésekkel. Úgy ítéli meg, a csomópont minden üzembe helyezett entitások virtuális szülője. Ezen a csomóponton üzembe helyezett entitásokat lekérdezések keresztül érhetők el, ha a csomópont állapotúként jelentette-e által System.FM az ugyanazon az entitások társított példányt. System.FM jelenti, hogy amikor a csomópont nem működik, vagy újraindul, egy új példányt a health Store adatbázisban automatikusan törli a az üzembe helyezett entitások, amelyek csak a lefelé csomópontján vagy a csomópont előző előfordulását is létezik.

* **SourceId**: System.FM
* **Vlastnost**: Állapot.
* **További lépések**: Ha a csomópont nem működik a frissítéshez, azt kell térjen vissza után van frissítve lett. Ebben az esetben az állapot kell váltson vissza az OK gombra. Ha a csomópont nem tér vissza, vagy nem sikerül, a probléma további vizsgálat van szüksége.

Az alábbi példa bemutatja a csomópont állapota OK állapotú System.FM esemény:

```powershell
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


### <a name="certificate-expiration"></a>Tanúsítvány lejárata
**System.FabricNode** jelentések figyelmeztetés, ha a csomópont által használt tanúsítványok lejáró. Nincsenek csomópontok három tanúsítvány: **Certificate_cluster**, **Certificate_server**, és **Certificate_default_client**. Ha legalább két héttel a lejárat, a jelentés állapot rendben. Ha a lejárati két héten belül, a jelentés típus egy figyelmeztetés. Ezek az események TTL végtelen, és azok törlődnek, amikor egy csomópont kikerül a fürtöt.

* **SourceId**: System.FabricNode
* **Vlastnost**: Kezdődik **tanúsítvány** és további információkat olvashat a tanúsítvány típusát.
* **További lépések**: A tanúsítványok frissítése, ha azok lejáró.

### <a name="load-capacity-violation"></a>Betöltési kapacitás megsértése
A Service Fabric terheléselosztó jelentések figyelmeztetés, ha azt észleli, hogy egy csomópont kapacitássértést.

* **SourceId**: System.PLB
* **Vlastnost**: Kezdődik **kapacitás**.
* **További lépések**: Ellenőrizze a megadott metrikák, és megtekintheti a pillanatnyi kapacitása a csomóponton.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Erőforrás-szabályozási mérőszámok csomópont kapacitás tér el
A jelentések System.Hosting egy figyelmeztetés, ha meghatározott fürt(ök) a fürtjegyzék nagyobbak, mint a következő fürt(ök) valós erőforrás-szabályozási metrikák (memória és CPU-magok). Egy jelentés jelenik meg, ha az első service-csomag, amely használja [erőforrás-szabályozás](service-fabric-resource-governance.md) regisztrálása egy adott csomóponton.

* **SourceId**: System.Hosting
* **Vlastnost**: **ResourceGovernance**.
* **További lépések**: A probléma problémát jelenthetnek, mivel szabályozó szolgáltatáscsomagok nem a várt módon kényszerített és [erőforrás-szabályozás](service-fabric-resource-governance.md) nem megfelelően működik. Frissítse a fürtjegyzék a következő metrikákhoz, a megfelelő fürt(ök) vagy nem adja meg azokat, és lehetővé teszik a Service Fabric automatikusan észleli a rendelkezésre álló erőforrások.

## <a name="application-system-health-reports"></a>Alkalmazás rendszerállapot-jelentések
System.CM, amely a kezelő szolgáltatás, akkor a szolgáltató, amely felügyeli az alkalmazással kapcsolatos információkat.

### <a name="state"></a>Állapot
System.CM kiderítheti OK gombra az alkalmazás létrehozásakor vagy frissítésekor. A health Store adatbázisban, értesíti arról, ha az alkalmazást törlik, hogy el kell távolítani az áruházból.

* **SourceId**: System.CM
* **Vlastnost**: Állapot.
* **További lépések**: Ha az alkalmazás létrehozás vagy frissítés, a kezelő állapotjelentés tartalmaznia kell. Ellenkező esetben ellenőrizze az alkalmazás állapota a lekérdezés kiállításával. Ha például a PowerShell-parancsmagot használhatja **Get-ServiceFabricApplication - ApplicationName** *applicationName*.

Az alábbi példa bemutatja az állapot esemény a a **fabric: / WordCount** alkalmazás:

```powershell
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
System.FM, amely jelöli meg a Feladatátvevőfürt-kezelő szolgáltatás, akkor a szolgáltató által felügyelt szolgáltatások adatait.

### <a name="state"></a>Állapot
System.FM kiderítheti OK gombra a szolgáltatás létrehozásakor. Az entitás törli a health Store adatbázisban, amikor törli a szolgáltatást.

* **SourceId**: System.FM
* **Vlastnost**: Állapot.

Az alábbi példa bemutatja az állapot eseményt a szolgáltatás **fabric: / WordCount/WordCountWebService**:

```powershell
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

### <a name="service-correlation-error"></a>Hiba a szolgáltatás korelace
**System.PLB** hibát jelez, ha azt észleli, hogy egy szolgáltatás frissítése akkor is vonatkozhatnak, és egy másik szolgáltatás, amely létrehozza az affinitási lánc jönne. A jelentés a sikeres frissítés történik, ha nincs bejelölve.

* **SourceId**: System.PLB
* **Vlastnost**: **ServiceDescription**.
* **További lépések**: Ellenőrizze a kapcsolódó szolgáltatások leírásai.

## <a name="partition-system-health-reports"></a>Partíció rendszerállapot-jelentések
System.FM jelöli meg a Feladatátvevőfürt-kezelő szolgáltatás, amely a szolgáltató, amely kezeli a szolgáltatás partícióinak információ.

### <a name="state"></a>Állapot
System.FM kiderítheti OK a partíció létrejött, és kifogástalan. Az entitás törli a a health store adatbázisból a partíció törlése.

A partíció nem éri a replikakészlet minimális száma, ha hiba jelenti. Ha a partíció nem a replikakészlet minimális száma alatt áll, de a cél replika száma alatt legyen, jelentést készít egy figyelmeztetés. Ha a partíció kvórumveszteségben van, System.FM hibát jelez.

Egyéb jelentős események figyelmeztetés közé tartozik, amikor az újrakonfigurálás hosszabb időt vesz igénybe a vártnál, és amikor a vártnál hosszabb ideig tart a build. A várt alkalommal a build és a újrakonfigurálás konfigurálható a következők alapján a szolgáltatás. Például ha egy szolgáltatás állapotát, például az Azure SQL Database, egy terabájt a build hosszabb időt vesz igénybe, mint a szolgáltatás, amely kisebb mennyiségű állapota.

* **SourceId**: System.FM
* **Vlastnost**: Állapot.
* **További lépések**: Ha az állapot nem OK, akkor lehet, hogy egyes másodpéldányok nem létrehozott, megnyitott, vagy nem megfelelően előléptetett elsődleges vagy másodlagos. 

Ha a leírás a kvórum elvesztése írja le, majd vizsgálata folyamatban van a részletes állapotjelentése elérhetetlenné replikákról és biztonsági mentése segítségével ahhoz, hogy a partíció hozza őket ismét elérhető.

Ha leírása ismerteti egy partíció ragadt [újrakonfigurálás](service-fabric-concepts-reconfiguration.md), majd az állapotjelentés az elsődleges replikán található további információkkal szolgál.

Más System.FM rendszerállapot-jelentések a replikákat, vagy a partíció vagy egyéb rendszerösszetevők szolgáltatást a jelentések lenne. 

Az alábbi példák egyes ezeket a jelentéseket ismertetik. 

Az alábbi példa bemutatja egy megfelelő partíció:

```powershell
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

Az alábbi példa bemutatja egy partíció, amely alatt a cél replika száma állapotát. A következő lépés, hogy a partíció leírást, amely bemutatja, hogyan van konfigurálva kap: **MinReplicaSetSize** három és **TargetReplicaSetSize** hét. A fürtben, amely ebben az esetben az öt kérje le a csomópontok számát. Tehát ebben az esetben két replika nem helyezhető, mert a cél replikák száma nagyobb, mint a rendelkezésre álló csomópontok száma.

```powershell
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

Az alábbi példa bemutatja egy partíciót, amely elakadt állapotát az újrakonfigurálás miatt nem érvényesítenie a megszakítás a felhasználó a token a **RunAsync** metódust. Az állapotjelentés bármely megjelölt az elsődleges replika (P) kivizsgálása segítségével részletes le további, a probléma.

```powershell
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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Az állapotjelentés megjeleníti az állapotát a replikák a partíció újrakonfigurálás alatt áll: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Minden egyes replikának a jelentés tartalmazza:
- Korábbi configuration szerepkör
- Aktuális konfiguráció szerepkör
- [Replika állapota](service-fabric-concepts-replica-lifecycle.md)
- A csomópont, amelyen a replika fut.
- Másodpéldány-azonosító:

A példához hasonlóan esetben további vizsgálat szükséges. A replikák megjelölve kezdve minden egyes replika állapotának vizsgálata `Primary` és `Secondary` (131482789658160654 és 131482789688598467) az előző példában.

### <a name="replica-constraint-violation"></a>Replika korlátozás megsértése
**System.PLB** jelentések figyelmeztetés, ha egy replika korlátozássértést észleli és az összes partícióreplikák nem helyezhető el. A jelentés részleteit jeleníti meg, milyen korlátozások, és tulajdonságok megakadályozhatja, hogy a replika elhelyezéskor.

* **SourceId**: System.PLB
* **Vlastnost**: Kezdődik **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Replika rendszerállapot-jelentések
**System.RA**, amely jelöli az újrakonfigurálási ügynök összetevő a szolgáltató számára a replika állapota.

### <a name="state"></a>Állapot
System.RA jelentések OK, a replika létrehozása után.

* **SourceId**: System.RA
* **Vlastnost**: Állapot.

Az alábbi példa bemutatja a kifogástalan állapotú replika:

```powershell
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
Ez a tulajdonság jelzi a figyelmeztetések vagy hibák, nyissa meg a replikát, zárja be a replika vagy egy másikra egy szerepkört a replika átmeneti megkísérlésekor szolgál. További információkért lásd: [replika életciklus](service-fabric-concepts-replica-lifecycle.md). Előfordulhat, hogy a hibák az API-hívások vagy a ebben az időszakban a szolgáltatás gazdafolyamat összeomlások által létrehozott kivételek gyűjtését. A hiba oka, hogy API hívásait C# kód, a Service Fabric az állapotjelentés ad hozzá a kivétel- és stack nyomkövetést.

Figyelmeztetések egészség művelet helyileg bizonyos számú alkalommal (függően házirend) újrapróbálkozás után aktiválódnak. Service Fabric újból próbálkozik a művelet akár egy maximális határérték. A maximális küszöb elérése után, próbálja meg ahhoz, hogy a probléma megoldásához. Ez a kísérlet első törölve, a művelet ezen a csomóponton nyújtja ezeket a figyelmeztetéseket okozhat. Például ha egy replika a csomópont megnyitása sikertelen, a Service Fabric health figyelmeztetés vet fel. Ha nem szűnik meg a replika nem nyílik meg, a Service Fabric helyi javítására szolgál. Ez a művelet is járhat, próbálkozzon egy másik csomóponton ugyanazt a műveletet. Ez a kísérlet hatására a figyelmeztetés ennél a replikánál törölni kell emelni. 

* **SourceId**: System.RA
* **Vlastnost**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, és **ReplicaChangeRoleStatus**.
* **További lépések**: Vizsgálja meg a szolgáltatást kód vagy az összeomlási memóriaképek azonosításához, ezért a művelet sikertelen.

Az alábbi példa bemutatja egy replikát, amely szűrész állapotát `TargetInvocationException` a nyitott metódus. A leírása tartalmazza a hiba jelentkezése, **IStatefulServiceReplica.Open**, a kivétel típusát **TargetInvocationException**, és a híváslánc.

```powershell
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

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

Az alábbi példa bemutatja egy replikát, folyamatosan összeomló bezárása során:

```powershell
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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguration
Ez a tulajdonság jelzi, ha egy replika végrehajtása szolgál egy [újrakonfigurálás](service-fabric-concepts-reconfiguration.md) észleli, hogy az újrakonfigurálás leállását vagy elakadt. Előfordulhat, hogy a jelentés a replikát, amelynek jelenlegi szerepkör elsődleges, kivéve a lapozófájl-kapacitás elsődleges újrakonfigurálás esetekben, ahol lehet a lefokozni elsődleges aktív másodlagos replikán.

Az újrakonfigurálás elakadt a is a következő okok valamelyike:

- Egy műveletet a helyi replika, a ugyanazt az újrakonfigurálás elvégzése egy replikát feladatom befejezése meghiúsul. Ebben az esetben a rendszerállapot-jelentések a replikát a más összetevők vizsgálata, System.RAP vagy System.RE, előfordulhat, hogy az további információkkal.

- Egy távoli adatforráson nem befejezi a műveletet. Az állapotjelentés felsorolt replikákat, amelynek műveletek folyamatban. A rendszerállapot-jelentések ezeket távoli replikákra vonatkozó további vizsgálat kell elvégezni. Emellett ez a csomópont és a távoli csomópont közötti kommunikációs probléma lehet.

Bizonyos ritkán előforduló esetekben az újrakonfigurálás is elakadt miatt kommunikáció vagy egyéb problémák, ez a csomópont és a Feladatátvevőfürt-kezelő szolgáltatás között.

* **SourceId**: System.RA
* **Vlastnost**: Az újrakonfigurálás.
* **További lépések**: Keresse meg a helyi vagy távoli replikák attól függően, a jelentés leírását.

Az alábbi példa bemutatja egy jelentés, ahol a helyi replika egy újrakonfigurálás elakadt. Ebben a példában, van egy szolgáltatás miatt nem érvényesítenie a megszakítás jogkivonatot.

```powershell
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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

A következő példa bemutatja az egészségügyi jelentés, ha egy újrakonfigurálás elakadt két távoli replika válaszára való várakozás. Ebben a példában nincsenek három replika készül a partíció, beleértve az aktuális elsődleges. 

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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

A jelentés azt mutatja, hogy az újrakonfigurálás elakadt két replika válaszára való várakozás: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Minden replika van megadva a következő információkat:
- Korábbi configuration szerepkör
- Aktuális konfiguráció szerepkör
- [Replika állapota](service-fabric-concepts-replica-lifecycle.md)
- Csomópont-azonosító
- Másodpéldány-azonosító:

Az újrakonfigurálás tiltásának feloldása:
- A **le** replikák kell kapcsolni. 
- A **található inbuild** replikák kell végezze el a build és a kész átmenet.

### <a name="slow-service-api-call"></a>Lassú szolgáltatás API-hívás
**System.RAP** és **System.Replicator** figyelmeztetés jelentést ad, ha a felhasználó kódjának hívás hosszabb időt vesz igénybe, mint a beállított idő. A figyelmeztetés nincs bejelölve, a hívás befejezése után.

* **SourceId**: System.RAP vagy System.Replicator
* **Vlastnost**: A lassú API neve. A leírás további információt nyújt az API-t óta függőben lévő idő.
* **További lépések**: Vizsgálja meg, miért érdemes a hívás hosszabb időt vesz igénybe a vártnál.

Az alábbi példa bemutatja az egészségügyi esemény System.RAP a egy megbízható szolgáltatás, amely nem kell érvényesítenie a megszakítás tokent **RunAsync**:

```powershell
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

A tulajdonság és a szöveg adja meg, melyik API van elakadt. A következő lépéseket a különböző elakadt API-k eltérőek. Az API a *IStatefulServiceReplica* vagy *IStatelessServiceInstance* van általában a szolgáltatás kódot tartalmaz hibát. Az alábbi szakasz ismerteti, hogyan ezek fordítani a [Reliable Services-modell](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: Ez a figyelmeztetés azt jelzi, hogy a hívás `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, vagy ha bírálja felül, `OnOpenAsync` elakadt.

- **IStatefulServiceReplica.Close** és **IStatefulServiceReplica.Abort**: A leggyakoribb eset szolgáltatása nem érvényesítenie a függvénynek átadott megszakítás token `RunAsync`. Lehet, hogy `ICommunicationListener.CloseAsync`, vagy ha bírálja felül, `OnCloseAsync` elakadt.

- **IStatefulServiceReplica.ChangeRole (S)** és **IStatefulServiceReplica.ChangeRole(N)**: A leggyakoribb eset szolgáltatása nem érvényesítenie a függvénynek átadott megszakítás token `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: A leggyakoribb eset az, hogy a szolgáltatás nem adott vissza egy feladat `RunAsync`.

A rendszer más API-hívás, amely képes elakadnak a **IReplicator** felületet. Példa:

- **IReplicator.CatchupReplicaSet**: Ez a figyelmeztetés azt jelzi, hogy két dolog egyikét. Nincsenek replikák mentése elégtelen. Ha szeretné látni, ha ez a helyzet, tekintse meg a replika állapota a partíció vagy az System.FM állapotjelentés egy az újrakonfigurálás elakadt a replikán. Vagy a replikák nem vannak bosszankodnak műveleteket. A PowerShell-parancsmag `Get-ServiceFabricDeployedReplicaDetail` minden replika állapotának meghatározásához használható. A probléma van replikák azon `LastAppliedReplicationSequenceNumber` értéke az elsődleges mögött `CommittedSequenceNumber` értéket.

- **IReplicator.BuildReplica(<Remote ReplicaId>)**: Ez a figyelmeztetés az összeállítási folyamat kapcsolatos problémát jelez. További információkért lásd: [replika életciklus](service-fabric-concepts-replica-lifecycle.md). Érdemes lehet egy replikáló címének helytelen konfiguráció miatt. További információkért lásd: [állapotalapú Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md) és [erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md). A távoli csomóponton hiba is lehet.

### <a name="replicator-system-health-reports"></a>A replikáló rendszerállapot-jelentések
**Replikációs várólistája megtelt:**
**System.Replicator** jelentések figyelmeztetés, ha a replikációs sor megtelt. Az elsődleges a replikációs várólistán általában megtelik, mert egy vagy több másodlagos replikát lassúak múlva nyugtázza a műveleteket. A másodlagos Ez általában akkor történik, ha a szolgáltatás a alkalmazni a műveletek lassú. A figyelmeztetés törlődik, ha a várólista már nem teljes.

* **SourceId**: System.Replicator
* **Vlastnost**: **PrimaryReplicationQueueStatus** vagy **SecondaryReplicationQueueStatus**, attól függően, a replika szerepkör.
* **További lépések**: Ha a jelentés az elsődleges, a fürtben lévő csomópontok közötti kapcsolat ellenőrzése. Ha az összes kapcsolat kifogástalan, előfordulhat, hogy egy magas késleltetésű műveletek alkalmazásához legalább egy lassú másodlagost lehet. Ha a jelentés a másodlagos, először ellenőrizze a lemez használatának és teljesítményének a csomóponton. Ezután ellenőrizze a kimenő kapcsolat a lassú csomópont és az elsődleges.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** figyelmeztetés az elsődleges replikán található jelentések, amikor egy másodlagos (távoli) replikátorral való kapcsolat állapota nem kifogástalan. A távoli replikátorral való cím akkor jelenik meg a jelentés üzenetet, amely megkönnyíti a helytelen konfiguráció lett átadva a vagy hálózati probléma merül fel a gyártóitól között.

* **SourceId**: System.Replicator
* **Vlastnost**: **RemoteReplicatorConnectionStatus**.
* **További lépések**: Tekintse meg a hibaüzenetet, és ellenőrizze, hogy a távoli replikátorral való címe megfelelően van konfigurálva. Például ha a távoli replikátorral való meg van nyitva, a "localhost" figyelési címmel, nem kívülről elérhető legyen. Ha a cím helyes, ellenőrizze az elsődleges csomópont és a távoli cím érintő lehetséges hálózati problémák kereséséhez közötti kapcsolat.

### <a name="replication-queue-full"></a>Replikációs sor megtelt
**System.Replicator** jelentések figyelmeztetés, ha a replikációs sor megtelt. Az elsődleges a replikációs várólistán általában megtelik, mert egy vagy több másodlagos replikát lassúak múlva nyugtázza a műveleteket. A másodlagos Ez általában akkor történik, ha a szolgáltatás a alkalmazni a műveletek lassú. A figyelmeztetés törlődik, ha a várólista már nem teljes.

* **SourceId**: System.Replicator
* **Vlastnost**: **PrimaryReplicationQueueStatus** vagy **SecondaryReplicationQueueStatus**, attól függően, a replika szerepkör.

### <a name="slow-naming-operations"></a>Lassú elnevezési műveletek
**System.NamingService** állapotáról jelentést az elsődleges replikán, amikor egy elnevezési művelet hosszabb időt vesz igénybe, mint elfogadható. Példák elnevezési operations [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) vagy [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Több módszert FabricClient területen találhatók. Például, hogy területen található [szolgáltatás-felügyeleti módszerek](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) vagy [tulajdonságot felügyeleti módszerek](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Az elnevezési szolgáltatásban szolgáltatásnevek megszünteti a fürt egy helyre. Felhasználók használhatják, kezelheti a szolgáltatás nevét és tulajdonságait. Service Fabric-particionált megőrzött szolgáltatás. Egyik partíciója jelöli a *Authority Owner*, amely tartalmazza az összes Service Fabric-nevek és szolgáltatásokkal kapcsolatos metaadatok. A Service Fabric-nevek vannak leképezve a különböző partíciók nevű *Name Owner* particionálja, így a szolgáltatás nem bővíthető. Tudjon meg többet a [Naming service](service-fabric-architecture.md).
> 
> 

Egy elnevezési művelet a vártnál több időt vesz igénybe, ha a művelet a Naming service partíció, amely kiszolgálja a művelet az elsődleges replikán figyelmeztető jelentésben meg van jelölve. Ha a művelet sikeresen befejeződik, a figyelmeztetés nincs bejelölve. Ha a művelet befejeződik, egy hiba miatt, a jelentés tartalmazza a hiba részleteinek.

* **SourceId**: System.NamingService
* **Vlastnost**: A előtaggal kezdődik "**Duration_**", és azonosítja a lassú művelet és a Service Fabric neve, amelyen a művelet történik. Például ha a szolgáltatás létrehozása a következő néven **fabric: / MyApp/MyService** túl sokáig tart, a tulajdonság értéke **Duration_AOCreateService.fabric:/MyApp/MyService**. A szerepkör az elnevezési partíció ezt a nevet és a művelet a "AO" mutat.
* **További lépések**: Ellenőrizze, hogy miért érdemes az elnevezési művelet sikertelen lesz. Minden művelet különböző oka lehet. Például a törlés szolgáltatás előfordulhat, hogy elakadt. A szolgáltatást, mert a gazda tartja összeomlik, egy csomóponton, a szolgáltatás kód egy felhasználói hiba miatt előfordulhat, hogy elakadt.

Az alábbi példa bemutatja a szolgáltatás létrehozási művelet. A művelet a megadott időtartamnál hosszabb időt vett igénybe. "AO" újrapróbálkozik, és elküldi a munka a "nem". "Nem" befejezni az utolsó művelet időkorlát. Ebben az esetben a azonos replika nem elsődleges, a "AO" és "Nem" szerepköröket.

```powershell
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
**System.Hosting** a szervezet az üzembe helyezett entitásokon.

### <a name="activation"></a>Aktiválás
System.Hosting kiderítheti OK gombra, ha egy alkalmazás aktiválása megtörtént a csomóponton. Ellenkező esetben hibát jelez.

* **SourceId**: System.Hosting
* **Vlastnost**: **Az aktiválás**, beleértve a bevezetési verziót.
* **További lépések**: Ha az alkalmazás állapota nem megfelelő, vizsgálja meg, miért érdemes az aktiválás nem sikerült.

Az alábbi példa bemutatja egy sikeres aktiválás:

```powershell
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
System.Hosting hibát jelez, ha az alkalmazás-csomag letöltése sikertelen lesz.

* **SourceId**: System.Hosting
* **Vlastnost**: **Töltse le**, beleértve a bevezetési verziót.
* **További lépések**: Vizsgálja meg, miért nem sikerült a letöltés a csomóponton.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage rendszerállapot-jelentések
**System.Hosting** a szervezet az üzembe helyezett entitásokon.

### <a name="service-package-activation"></a>Szolgáltatási csomag aktiválása
System.Hosting rendben, jelentések, ha a csomóponton a szolgáltatási csomag aktiválás sikeresen végbemegy. Ellenkező esetben hibát jelez.

* **SourceId**: System.Hosting
* **Vlastnost**: Aktiválás.
* **További lépések**: Vizsgálja meg, miért érdemes az aktiválás nem sikerült.

### <a name="code-package-activation"></a>Kód az alkalmazáscsomag aktiválása
System.Hosting kiderítheti OK minden kódcsomaghoz Ha az aktiválás sikeresen végbemegy. Ha az aktiválás sikertelen, jelentést készít egy figyelmeztetés konfigurálva. Ha **CodePackage** nem tudja aktiválni, vagy nagyobb, mint a beállított hibával ér véget **CodePackageHealthErrorThreshold**, üzemeltetési hibát jelez. Ha egy szolgáltatáscsomag kód több csomagot tartalmaz, egy aktiválási jelentés minden egyes jön létre.

* **SourceId**: System.Hosting
* **Vlastnost**: Az előtag- **CodePackageActivation** , és tartalmazza a nevét, a belépési pont és a kódcsomag *CodePackageActivation:CodePackageName:SetupEntryPoint / EntryPoint*. Ha például **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Szolgáltatási típus regisztrációs
System.Hosting jelentések OK, ha a szolgáltatás típusának regisztrálása sikerült. Hibát jelez, ha a regisztráció nem volt meg időben, használatával konfigurált **ServiceTypeRegistrationTimeout**. Ha a modul le van zárva, a szolgáltatás típusának regisztrációját a csomópontból és üzemeltetési jelentések figyelmeztetést.

* **SourceId**: System.Hosting
* **Vlastnost**: Az előtag- **ServiceTypeRegistration** , és tartalmazza a szolgáltatás neve. Ha például **ServiceTypeRegistration:FileStoreServiceType**.

Az alábbi példa bemutatja egy megfelelő telepített szervizcsomag:

```powershell
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
System.Hosting hibát jelez, ha a service-csomag letöltése sikertelen lesz.

* **SourceId**: System.Hosting
* **Vlastnost**: **Töltse le**, beleértve a bevezetési verziót.
* **További lépések**: Vizsgálja meg, miért nem sikerült a letöltés a csomóponton.

### <a name="upgrade-validation"></a>Frissítésének ellenőrzése
System.Hosting hibát jelez, ha a frissítés során az érvényesítés meghiúsul, vagy ha a frissítés sikertelen lesz, a csomóponton.

* **SourceId**: System.Hosting
* **Vlastnost**: Az előtag- **FabricUpgradeValidation** , és tartalmazza a frissített verzió.
* **Leírás**: Az észlelt hiba mutat.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Nem definiált csomópont kapacitását az erőforrás-szabályozási mérőszámok
System.Hosting jelentések figyelmeztetés, ha fürt(ök) a fürtjegyzék nem definiált, és a konfiguráció automatikus észlelési ki van kapcsolva. Service Fabric vet fel, hogy egy állapotfigyelő figyelmeztetés, amikor a csomagnak használó [erőforrás-szabályozás](service-fabric-resource-governance.md) regisztrálása egy adott csomóponton.

* **SourceId**: System.Hosting
* **Vlastnost**: **ResourceGovernance**.
* **További lépések**: A probléma megoldásához az előnyben részesített módja, hogy módosítsa a fürtjegyzék elérhető erőforrások automatikus észlelésének engedélyezése. Egy másik lehetőség, hogy frissítse a fürtjegyzék megfelelően megadott fürt(ök) számára ezeket a metrikákat.

## <a name="next-steps"></a>További lépések
* [A Service Fabric-állapotjelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

* [Hogyan lehet szolgáltatási állapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [A szolgáltatások helyi monitorozása és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

