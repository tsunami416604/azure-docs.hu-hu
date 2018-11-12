---
title: Megtekintése az Azure Service Fabric-entitások összesített állapotának |} A Microsoft Docs
description: Ismerteti, hogyan lehet lekérdezni, megtekintése és az Azure Service Fabric-entitások összesített állapotának állapotlekérdezések és általános lekérdezések kiértékelése.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: b6f6653381b5fcf80b9647c64334dfed1a2230bb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230852"
---
# <a name="view-service-fabric-health-reports"></a>A Service Fabric-állapotjelentések megtekintése
Az Azure Service Fabric mutatja be egy [állapotmodell](service-fabric-health-introduction.md) egészségügyi entitásokkal, mely rendszer összetevőit és watchdogs is jelentés helyi feltételek, amelyek figyelése. A [health Store adatbázisban](service-fabric-health-introduction.md#health-store) összesíti az összes egészségügyi adatokat annak meghatározására, hogy e entitások kifogástalan állapotú.

A fürt rendszer automatikusan kitölti a rendszer összetevők által küldött rendszerállapot-jelentések. További információk: [rendszerállapot-jelentések használata a hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

A Service Fabric több módszert is biztosít az entitások összesített állapotának beolvasásához:

* [A Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vagy egyéb vizualizációs eszközök
* Állapotlekérdezések (a PowerShell, az API-t vagy a REST)
* Általános lekérdezi, hogy visszatérési entitások listáját, amelyek állapota a Tulajdonságok (a PowerShell, az API-t vagy a REST) egyik

Ezek a lehetőségek bemutatása érdekében használjuk a helyi fürt öt csomóponttal rendelkező és a [fabric: / WordCount alkalmazás](https://aka.ms/servicefabric-wordcountapp). A **fabric: / WordCount** tartalmazó két alapértelmezett szolgáltatást, egy állapotalapú szolgáltatás típusú alkalmazás `WordCountServiceType`, és a egy állapotmentes szolgáltatás típusú `WordCountWebServiceType`. Módosítottam a `ApplicationManifest.xml` hétrészes sorozat cél replikái az állapotalapú szolgáltatásból, és a egy partíció szükséges. Mivel a fürt csak öt csomópontot, a rendszer összetevőinek jelentést egy figyelmeztetés a szolgáltatás partíció, mert a célként megadott száma alatt legyen.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>A Service Fabric Explorerben állapota
Service Fabric Explorert a fürtben visual nézetét jeleníti meg. Az alábbi képen láthatja, hogy:

* Az alkalmazás **fabric: / WordCount** piros (a hiba), egy hibaesemény által jelzett, mert **MyWatchdog** tulajdonság **rendelkezésre állási**.
* A szolgáltatások egyik **fabric: / WordCount/WordCountService** sárga (a figyelmeztetés). A szolgáltatás úgy van konfigurálva, a hét replikákat, és a fürt öt csomópontjának rendelkezik, ezért a két repicas nem helyezhető el. Bár ez nem látható itt, a szolgáltatás partíciója sárga miatt a rendszer jelentést `System.FM` arról, hogy `Partition is below target replica or instance count`. A sárga partíció a sárga szolgáltatás aktiválása.
* A fürt piros a piros alkalmazás miatt.

A kiértékelés a fürtjegyzék és alkalmazásjegyzék alapértelmezett házirendeket használ. Szigorú házirendek, és nem minden hiba működését.

A fürthöz a Service Fabric Explorerrel nézete:

![A fürthöz a Service Fabric Explorerrel nézetét.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Tudjon meg többet [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Állapotlekérdezések
A Service Fabric állapotlekérdezések mutatja az egyes támogatott [entitástípusok](service-fabric-health-introduction.md#health-entities-and-hierarchy). A módszerekkel, az API-n keresztül hozzáférhetők [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell-parancsmagok és a többi. Ezeket a lekérdezéseket az entitás teljes állapota információt ad vissza: az összesített állapotát, entitás hálózatállapot-események, gyermek állapotokat (ha alkalmazható), nem megfelelő értékelések (ha az entitás állapota nem kifogástalan) és gyermekek egészségügyi statisztika (Ha érvényes).

> [!NOTE]
> Amikor teljesen megjelenik a a health Store adatbázisban egy állapotfigyelő entitást ad vissza. Az entitás aktív (nem törölt) legyen, és a rendszer a jelentés rendelkezik. A hierarchia lánc szülő entitásai is rendelkeznie kell a rendszer jelentéseket. Ha bármely feltétel nem teljesül, az egészségügyi lekérdezi a visszaadandó egy [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) a [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` megtudhatja, miért érdemes az entitás nem adott vissza.
>
>

Az állapotlekérdezések a entitásának azonosítója, amely az entitás típusa attól függ, át kell. A lekérdezések nem kötelező egészségügyi szabályzatparaméterek fogadja el. Ha nincsenek házirendek meg van adva, a [állapotházirendeket](service-fabric-health-introduction.md#health-policies) a fürt vagy az alkalmazás-jegyzékfájlból szolgálnak az értékeléshez. Ha a jegyzékek állapotházirendeket definícióját nem tartalmaz, az alapértelmezett házirendek kiértékelése szolgálnak. Az alapértelmezett házirendek nem ugyan az esetleges hibákat. A lekérdezések elfogadhatja az adatszolgáltató csak részleges gyermekek vagy események – a megadott szűrők tiszteletben amelyekre szűrők is. Másik szűrő lehetővé teszi, hogy a gyermekek statisztikai adatainak kivételével.

> [!NOTE]
> A kimeneti alkalmazza a rendszer a kiszolgáló oldalán, az üzenet válasz mérete csökken. Javasoljuk, hogy Ön a kimeneti szűrők segítségével korlátozhatja a visszaküldött adatok ahelyett, hogy az ügyféloldalon szűrőket alkalmazhat.
>
>

Egy entitás állapotát tartalmazza:

* Az entitás összesített állapotát. A health Store adatbázisban entitás rendszerállapot-jelentések, a gyermek állapotokat (ha alkalmazható) és a házirendek alapján számítja ki. Tudjon meg többet [entitás állapotának kiértékelését](service-fabric-health-introduction.md#health-evaluation).  
* Az állapotesemények az entitásra.
* Az összes gyermekre a gyermekek is rendelkező entitások állapotokat gyűjteménye. Állapotokhoz entitás azonosítók és összesített állapotát tartalmazza. Gyermek teljes állapotának lekéréséhez hívja meg a lekérdezés állapota a gyermek entitástípus, és adja át a gyermek azonosítója.
* A nem megfelelő értékelések, amelyek a jelentést, amely kiváltotta az állapota, ha az entitás nem kifogástalan. Az értékelések rekurzív, a gyermekek egészségügyi értékeléseket, hogy a jelenlegi állapot aktiválva tartalmazó rendszer. Egy figyelő például elleni replika hibát jelzett. Az alkalmazás állapota miatt nem megfelelő állapotú szolgáltatás; nem megfelelő állapotú próbaverzióra jeleníti meg. a szolgáltatás állapota nem megfelelő, hiba; a partíció miatt a partíció állapota nem megfelelő, egy hiba; replika miatt a replika állapota nem megfelelő, a figyelő állapota hibajelentés miatt.
* Az entitások lehetnek gyermekei, minden gyermek típusú állapotának statisztikai adatait. Például a fürt állapotának alkalmazások, a szolgáltatások, a partíciókat, a replikák teljes számát jeleníti meg, és entitásokat a fürtben üzembe helyezett. Szolgáltatás állapotának a partíciókat és -replikákat a megadott szolgáltatás alatt teljes számát mutatja.

## <a name="get-cluster-health"></a>Fürtállapot beolvasása
A fürt entitás állapotát adja vissza, és tartalmazza az alkalmazások és csomópontok (a fürt children) állapotúak. Bemenet:

* [Opcionális] A csomópontok és a fürthöz kapcsolódó események értékeli ki, hogy fürt állapotházirend.
* [Opcionális] Az állapotfigyelő szabályzat alkalmazástérkép, felül az alkalmazásjegyzék szabályzatok állapot-szabályzatokkal.
* [Opcionális] Események, a csomópontok és az alkalmazásokat, amelyek adja meg, hogy mely tételek szűrők érdekesek lehetnek, és a rendszer visszalépteti az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Összes esemény, csomópontokat és az alkalmazások állapotellenőrzése entitáshoz összesítve, függetlenül a szűrő szolgálnak.
* [Opcionális] Szűrő kizárása egészségügyi statisztikák.
* [Opcionális] Szűrőjét fabric: / System health statisztika a health statisztika. Csak akkor alkalmazható, ha az egészségügyi statisztikák nem tartoznak ide. Alapértelmezés szerint az egészségügyi statisztikák csak a felhasználói alkalmazásoknak és nem a rendszer alkalmazás statisztikák közé tartozik.

### <a name="api"></a>API
Fürtállapot beolvasása, hozzon létre egy `FabricClient` hívja a [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metódust a **HealthManager**.

A következő hívást a fürtállapot beolvasása:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

A következő kódot a csomópontok és alkalmazások egy egyéni fürt állapotházirend és szűrők használatával a fürt állapotának beolvasása. Azt is meghatározza, hogy a health statisztika tartalmazza-e a fabric: / System statisztikákat. Hozza létre [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), amely tartalmazza a bemeneti adatokat.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A parancsmag a fürt állapotának beolvasásához [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot.

A fürt állapota öt csomópont, a rendszer az alkalmazás és fabric: / WordCount leírtak szerint konfigurálva.

Az alábbi parancsmagot az alapértelmezett házirendek fürtállapot beolvasása. Az összesített állapota figyelmet igényel, mert a fabric: / WordCount alkalmazás figyelmeztetés. Vegye figyelembe, hogy a nem megfelelő értékelések hogyan részletekkel szolgálnak a feltételeket, amelyeknek aktivált összesített állapotát.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                          
                          
NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_0
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

A következő PowerShell-parancsmagot a-egyéni alkalmazás-házirendek használatával a fürt állapotának beolvasása. Csak az alkalmazások és csomópontok hiba vagy figyelmeztetés eredményeket szűri. Ennek eredményeképpen nem csomópontok ad vissza, mivel ezek az összes kifogástalan állapotú. Csak a fabric: / WordCount alkalmazás tiszteletben tartja az alkalmazások szűrő. Mivel az egyéni házirend megadja, hogy fontolja meg a háló hibák, figyelmeztetések: / WordCount alkalmazás, az alkalmazás ki lesz értékelve, mint a hiba, és így az a fürt.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                          
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="rest"></a>REST
Megtekintheti a fürt állapota egy [GET kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-node-health"></a>Csomópont állapotának lekérése
Egy csomópont entitás állapotát adja vissza, és tartalmazza a csomóponton jelentett hálózatállapot-események. Bemenet:

* [Kötelező] A csomópont nevét, amely azonosítja a csomópontot.
* [Opcionális] A fürt állapotának használt csoportházirend-beállítások állapotának értékeléséhez.
* [Opcionális] Adja meg, hogy mely tételek események szűrők érdekesek lehetnek, és a rendszer visszalépteti az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Az összes esemény segítségével kiértékelheti az összesített entitás állapotát, függetlenül a szűrő.

### <a name="api"></a>API
A csomópontok állapotának lekérése az API-n keresztül, hozzon létre egy `FabricClient` hívja a [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) annak HealthManager metódust.

A következő kódot a csomópontok állapotának a megadott csomópont nevének beolvasása:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

A következő kód lekéri a megadott csomópont nevét a csomópontok állapotának és eseményszűrő és egyéni házirendet keresztül továbbítja [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A parancsmagot a csomópontok állapotának lekérése van [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot.
A következő parancsmagot a csomópontok állapotának lekérdezi az alapértelmezett házirendek:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

A következő parancsmagot a fürt összes csomópontja állapotának beolvasása:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Beszerezheti a csomópontok állapotának egy [GET kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-application-health"></a>Alkalmazás állapotának beolvasása
Egy alkalmazás entitás állapotát adja vissza. Az állapotokat az üzembe helyezett alkalmazás és szolgáltatás gyermekeket tartalmaz. Bemenet:

* [Kötelező] Az alkalmazás neve (URI), amely azonosítja az alkalmazást.
* [Opcionális] Az alkalmazás állapotszabályzata felül az alkalmazásjegyzék szabályzatok.
* [Opcionális] Az események, szolgáltatások és adja meg, hogy mely tételek üzembe helyezett alkalmazások szűrők érdekesek lehetnek, és a rendszer visszalépteti az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Összes esemény, szolgáltatások és telepített alkalmazások állapotellenőrzése entitáshoz összesítve, függetlenül a szűrő szolgálnak.
* [Opcionális] Szűrő kizárása az egészségügyi statisztikák. Ha nincs megadva, az egészségügyi statisztikák közé tartozik az ok, figyelmeztetés és hibák száma az összes alkalmazás számára: szolgáltatások, a partíciókat, a replikákat, üzembe helyezett alkalmazások és üzembe helyezett service-csomagok.

### <a name="api"></a>API
Alkalmazás állapotának beolvasása, hozzon létre egy `FabricClient` hívja a [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) annak HealthManager metódust.

A következő kódot az alkalmazás állapotáról a megadott alkalmazás neve (URI) lekérése:

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

A következő kód lekéri az alkalmazás állapotáról, az alkalmazásnév (URI), a szűrőket, és egyéni szabályzatok keresztül megadott [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A parancsmag az alkalmazás állapotának beolvasásához [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot.

A következő parancsmag állapotát adja vissza a **fabric: / WordCount** alkalmazás:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok
                                  
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning
                                  
DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok
                                  
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
                                  
HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

A következő PowerShell-parancsmag egyéni szabályzatokban adja át. A gyermekek és események is szűrik.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error
                                  
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Az alkalmazás állapotáról kaphat egy [GET-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-service-health"></a>Szolgáltatásállapot beolvasása
A szolgáltatás entitás állapotát adja vissza. A partíció állapotokat tartalmazza. Bemenet:

* [Kötelező] A szolgáltatásnév (URI), amely azonosítja a szolgáltatást.
* [Opcionális] Az alkalmazás állapotszabályzata felül az alkalmazásjegyzék házirend.
* [Opcionális] Események és partíciókat, hogy adja meg, hogy mely tételek szűrők érdekesek lehetnek, és a rendszer visszalépteti az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Összes esemény és a partíciók állapotellenőrzése entitáshoz összesítve, függetlenül a szűrő szolgálnak.
* [Opcionális] Szűrő kizárása egészségügyi statisztikák. Ha nem megadva, az egészségügyi statisztika megjelenítése az ok, figyelmeztetés, és hiba történt az összes partíciót és a szolgáltatás replikák száma.

### <a name="api"></a>API
Szolgáltatásállapot beolvasása az API-n keresztül, hozzon létre egy `FabricClient` hívja a [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) annak HealthManager metódust.

Az alábbi példa lekéri a megadott szolgáltatásnév (URI) a szolgáltatás állapotát:

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

A következő kód lekéri a szolgáltatás állapotát a megadott szolgáltatásnév (URI), a szűrők és egyéni szabályzatot [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A parancsmag beolvasni a szolgáltatás állapotát [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot.

A következő parancsmag lekérdezi a service health alapértelmezett állapotházirendeket használatával:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                        
                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                        
                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Megjelenik a service health használata az egy [GET kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-partition-health"></a>Get-partition health
Egy partíció entitás állapotát adja vissza. A replika állapotokat tartalmazza. Bemenet:

* [Kötelező] A partíció Azonosítóját (GUID), amely azonosítja a partíción.
* [Opcionális] Az alkalmazás állapotszabályzata felül az alkalmazásjegyzék házirend.
* [Opcionális] Szűrők eseményekhez, és adja meg, hogy mely tételek replikák érdekesek lehetnek, és a rendszer visszalépteti az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Összes esemény és a replikák állapotellenőrzése entitáshoz összesítve, függetlenül a szűrő szolgálnak.
* [Opcionális] Szűrő kizárása egészségügyi statisztikák. Ha nincs megadva, az egészségügyi a statisztikák hány replika van ok, figyelmeztetés és hiba állapotok.

### <a name="api"></a>API
Az API-n keresztül partition health beszerzéséhez hozzon létre egy `FabricClient` hívja a [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) annak HealthManager metódust. Adja meg a választható paraméterek, hozzon létre [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
A parancsmag a partition health beolvasásához [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot.

A következő parancsmag lekérdezi az összes partíció tartozó állapotot. a **fabric: / WordCount/WordCountService** szolgáltatás és a replika állapotokat kiszűri:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
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
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Megjelenik a partition health használata az egy [GET kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-replica-health"></a>Replika állapotának lekérése
Egy állapotalapú szolgáltatás replika- vagy egy állapotmentes szolgáltatás állapotát adja vissza. Bemenet:

* [Kötelező] A partíció Azonosítóját (GUID) és a replika azonosítója, amely azonosítja a replikát.
* [Opcionális] Az alkalmazás állapotának szabályzat paramétereit az alkalmazásjegyzék szabályzatok felül.
* [Opcionális] Adja meg, hogy mely tételek események szűrők érdekesek lehetnek, és a rendszer visszalépteti az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Az összes esemény segítségével kiértékelheti az összesített entitás állapotát, függetlenül a szűrő.

### <a name="api"></a>API
A replika állapota az API-n keresztül beszerzéséhez hozzon létre egy `FabricClient` hívja a [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) annak HealthManager metódust. Speciális paraméterek megadásához használja [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
A parancsmag beolvasni a replika állapota [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot.

A következő parancsmag lekérdezi az elsődleges replika számára a szolgáltatás összes partíció állapotát:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
A replika állapota megjelenik egy [GET kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-deployed-application-health"></a>Az üzembe helyezett alkalmazás állapotának beolvasása
Egy csomópont entitást a központi telepítésű alkalmazás állapotát adja vissza. Az üzembe helyezett szolgáltatás csomag állapotokat tartalmazza. Bemenet:

* [Kötelező] Az alkalmazás nevét (URI) és a csomópont neve (karakterlánc), amelyek azonosítják az üzembe helyezett alkalmazás.
* [Opcionális] Az alkalmazás állapotszabályzata felül az alkalmazásjegyzék szabályzatok.
* [Opcionális] Szűrők eseményekhez, és adja meg, hogy mely tételek telepített szervizcsomagok érdekesek lehetnek, és a rendszer visszalépteti az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Összes esemény és a telepített szervizcsomagok állapotellenőrzése entitáshoz összesítve, függetlenül a szűrő szolgálnak.
* [Opcionális] Szűrő kizárása egészségügyi statisztikák. Ha nincs megadva, az egészségügyi statisztika megjelenítése telepített szervizcsomagok száma ok, figyelmeztetés és hiba állapotokat.

### <a name="api"></a>API
Az API-n keresztül a csomóponton üzembe helyezett alkalmazás állapotának beolvasása, hozzon létre egy `FabricClient` hívja a [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) annak HealthManager metódust. Választható paraméterek megadásához használja [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
A parancsmag a telepített alkalmazások állapotának beolvasásához [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot. Ismerje meg, ha egy alkalmazás központi telepítése, futtassa [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) , és tekintse meg az üzembe helyezett alkalmazás gyermekei.

A következő parancsmag lekérdezi az állapotát a **fabric: / WordCount** az üzembe helyezett alkalmazás **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM
                                     
HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Az üzembe helyezett alkalmazás állapotáról kaphat egy [GET kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-deployed-service-package-health"></a>Üzembe helyezett csomag szolgáltatásállapot beolvasása
Egy telepített szolgáltatást csomag entitás állapotát adja vissza. Bemenet:

* [Kötelező] Az alkalmazás nevét (URI), a csomópont neve (karakterlánc) és a service manifest, amelyek azonosítják a telepített service-csomag neve (karakterlánc).
* [Opcionális] Az alkalmazás állapotszabályzata felül az alkalmazásjegyzék házirend.
* [Opcionális] Adja meg, hogy mely tételek események szűrők érdekesek lehetnek, és a rendszer visszalépteti az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Az összes esemény segítségével kiértékelheti az összesített entitás állapotát, függetlenül a szűrő.

### <a name="api"></a>API
Az API-n keresztül telepített szervizcsomag állapotának lekéréséhez hozzon létre egy `FabricClient` hívja a [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) annak HealthManager metódust. Választható paraméterek megadásához használja [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
A parancsmagot, amely a telepített csomag szolgáltatásállapot beolvasása van [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot. Ha egy alkalmazás központi telepítése megtekintéséhez futtassa [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) , és tekintse meg a központilag telepített alkalmazások. Melyik szolgáltatást egy alkalmazásban vannak csomagok megtekintéséhez tekintse meg az üzembe helyezett szolgáltatás csomag gyermekeinek a [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) kimeneti.

A következő parancsmag lekérdezi az állapotát a **WordCountServicePkg** service-csomag, a **fabric: / WordCount** az üzembe helyezett alkalmazás **_Node_2**. Az entitás rendelkezik **System.Hosting** sikeres service-csomagot és -belépési ponttal aktiválási és sikeres szolgáltatástípus regisztráció jelentéseket.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Megjelenik a telepített csomag szolgáltatásállapot-egy [GET kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="health-chunk-queries"></a>Állapotlekérdezések Blok dat
Az adattömbök állapotlekérdezések többszintű fürt gyermekek (rekurzív módon), egy bemeneti szűrőket adhat vissza. Támogatja a speciális szűrők, amelyek lehetővé teszik nagy rugalmasságot biztosít a gyermekek visszaadandó megválasztásához. A szűrők gyermekek adhatja meg az egyedi azonosító, vagy egyéb csoport azonosítók és/vagy állapotokat. Alapértelmezés szerint a gyermekek nem szerepelnek, ellentétben az első szintű gyermekek mindig tartalmazó egészségügyi parancsokat.

A [állapotlekérdezések](service-fabric-view-entities-aggregated-health.md#health-queries) szűrők szükség szerint a megadott entitás csak az első szintű gyermekeit adja vissza. A gyermekek a gyermekek lekérése, meg kell hívni a további health API-k minden entitásnál a lényeges. Ehhez hasonlóan adott entitásokhoz állapotának lekéréséhez, meg kell hívnia egy állapotfigyelési API minden egyes kívánt entitás. Az adattömbök lekérdezés Speciális szűrés lehetővé teszi több elem is hasznos helyhez az egy lekérdezésben, minimálisra csökkentik az üzenet mérete és az üzenetek száma.

Az adattömbök lekérdezés értékét, hogy az képes állapot több fürt entitások (esetleg minden fürt entitások szükséges legfelső szintjén kezdve) egyetlen hívásával. Például fejezhető összetett egészségügyi lekérdezés:

* Visszatérési csak alkalmazások hibás, és azon alkalmazások esetében az összes szolgáltatás felvétel figyelmeztetés vagy hiba. A szolgáltatások tartalmazza az összes partíciót.
* Csak négy alkalmazások, a megadott névvel állapotát adja vissza.
* Csak a kívánt alkalmazástípus alkalmazások állapotát adja vissza.
* A csomópont összes üzembe helyezett entitások visszaadása. Minden alkalmazást, adja vissza a megadott csomóponton alkalmazások és az ezen a csomóponton üzembe helyezett service-csomagok üzembe helyezett.
* Hiba történt az összes replika visszaadása. Alkalmazások, szolgáltatások, a partíciók és csak a replikák hiba adja vissza.
* Vissza az összes alkalmazást. Egy megadott szolgáltatás összes partíció tartalmazza.

Az egészségügyi adattömbök lekérdezés jelenleg csak a fürt entitás van közzétéve. Azt adja vissza a fürt egészségügyi adattömb, amely tartalmazza:

* A fürt állapotát összesíti.
* Egészségügyi állapot adattömbök listája, amely a bemeneti szűrők tiszteletben csomópontok.
* Egészségügyi állapot adattömbök alkalmazások listáját, amely a bemeneti szűrők tiszteletben. Egyes application health állapot adattömbök adattömbök listáját tartalmazza, amely a bemeneti szűrőket és tiszteletben tartja a szűrők minden üzembe helyezett alkalmazások adattömbök listáját tiszteletben a többi szolgáltatáshoz. Ugyanaz a szolgáltatások és telepített alkalmazások számára. Ezzel a módszerrel a fürtben lévő összes entitáshoz potenciálisan visszaadható-Ha a rendszer kéri, hierarchikus struktúrában kapcsolódhatnak.

### <a name="cluster-health-chunk-query"></a>Fürt állapota adattömbök lekérdezés
A fürt entitás állapotát adja vissza, és a hierarchikus egészségügyi állapot adattömbök szükséges gyermekek tartalmazza. Bemenet:

* [Opcionális] A csomópontok és a fürthöz kapcsolódó események értékeli ki, hogy fürt állapotházirend.
* [Opcionális] Az állapotfigyelő szabályzat alkalmazástérkép, felül az alkalmazásjegyzék szabályzatok állapot-szabályzatokkal.
* [Opcionális] Csomópontok és alkalmazásokhoz, amelyek adja meg, hogy mely tételek szűrők érdekesek lehetnek, és a rendszer visszalépteti az eredményben. A szűrők egy entitás/csoport entitások jellemző vagy vonatkozó összes entitás azon a szinten. A szűrők listájának egy általános szűrési és/vagy a lekérdezés által visszaadott részletes entitások egyedi azonosítók szűrőket is tartalmazhat. Ha üres, a gyermekek alapértelmezés szerint nem jelzi.
  További információ a szűrőket [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) és [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Az alkalmazás szűrőket is rekurzív módon adja meg a speciális szűrők számára készült.

Az adattömbök eredmény tartalmazza a gyermekek tiszteletben a szűrőket.

Jelenleg az adattömbök lekérdezés nem ad vissza nem megfelelő értékelések vagy entitáshoz eseményeket. A további információk a meglévő fürt állapotának lekérdezés használatával szerezhető be.

### <a name="api"></a>API
Cluster health adattömbök beszerzéséhez hozzon létre egy `FabricClient` hívja a [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metódust a **HealthManager**. Beadható [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) állapotházirendeket írja le, és a speciális szűrők.

A következő kódot a speciális szűrők cluster health adattömbök beolvasása.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A parancsmag a fürt állapotának beolvasásához [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmagot.

A következő kód lekéri a csomópontok csak akkor, ha a hiba egy adott csomópont, amely minden esetben a rendszer visszalépteti kivételével vannak.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1
                               
                               NodeName              : _Node_1
                               HealthState           : Ok
                               
ApplicationHealthStateChunks : None
```

A következő parancsmag lekérdezi a fürt adatrészlet alkalmazás szűrőket.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1
                               
                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1
                               
                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5
                               
                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

A következő parancsmagot a csomópont minden üzembe helyezett entitásokat ad vissza.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2
                               
                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
                               
                               
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Megjelenik a fürt egészségügyi rendszer egy [GET kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) vagy egy [POST-kérés](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) , amely tartalmazza a házirendek és a szervezet leírt speciális szűrők.

## <a name="general-queries"></a>Általános lekérdezések
Általános lekérdezésekben, megadott típusú Service Fabric-entitások listáját adja vissza. Ezek az API-n keresztül érhetők el (a módszerek útján **FabricClient.QueryManager**), PowerShell-parancsmagok és a REST. Ezeket a lekérdezéseket a több összetevőt segédlekérdezések összesíteni. Az egyik a [health Store adatbázisban](service-fabric-health-introduction.md#health-store), amely feltölti az összesített állapota minden egyes lekérdezés eredménye.  

> [!NOTE]
> Általános lekérdezések az entitások összesített állapotának visszaadása, és nem tartalmaz részletes állapotadatok. Ha egy entitás állapota nem kifogástalan, követhesse az állapotlekérdezések az egészségügyi adatok, beleértve az eseményeket, valamint a gyermek állapotokat és a nem megfelelő értékelések beolvasásához.
>
>

Ha általános lekérdezések egy entitás egy ismeretlen állapot visszaállításához, lehetséges, hogy a health Store adatbázisban nem rendelkezik a entitás minden adatát. Is lehetőség, hogy a health Store adatbázisban a segédlekérdezés nem volt sikeres (például egy kommunikációs hiba vagy a health Store adatbázisban szabályozta). Az entitás állapotát lekérdezéssel követéséhez. Ha a segédlekérdezés átmeneti hibák, például a hálózati problémákat, a nyomon követési lekérdezés sikerülhet. Azt is kaphat további részleteket a arról, hogy miért érdemes az entitás nem érhető el a health store adatbázisból.

A lekérdezéseket tartalmazó **HealthState** entitásokat a következők:

* Csomópont-lista: a lista csomópontokat a fürtben (lapozható) adja vissza.
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Alkalmazások listája: a fürt (lapozható) alkalmazások listáját adja vissza.
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista: egy alkalmazás (lapozható) a szolgáltatások listáját adja vissza.
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partíciólista: (lapozható) szolgáltatás partícióinak listáját adja vissza.
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Replika listája: egy partíció (lapozható) replikák listáját adja vissza.
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Telepített alkalmazások listája: a csomópont a telepített alkalmazások listáját adja vissza.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Üzembe helyezett szolgáltatás csomaglista: az üzembe helyezett alkalmazás service-csomagok listáját adja vissza.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> A lekérdezések némelyikének a lapokra bontott eredményeket adja vissza. Ezeket a lekérdezéseket, a visszatérési származtatva listáját [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Az eredmények nem férnek el egy üzenetet, ha csak egy oldal adja vissza, és a egy continuationtoken argumentumot használja, amely nyomon követi, ahol enumerálás leállt. Továbbra is az ugyanazon lekérdezés és a folytatási token adhatók át az előző lekérdezést a következő eredmények eléréséhez.
>
>

### <a name="examples"></a>Példák
A következő kód lekéri a sérült alkalmazások a fürtben:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

A következő parancsmag lekérdezi az alkalmazás részletes a fabric: / WordCount alkalmazás. Figyelje meg, hogy állapot figyelmeztetés.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

A következő parancsmag lekérdezi a szolgáltatások állapota a hiba:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Fürt- és frissítések
A fürt és az alkalmazás megfigyelt frissítés során a Service Fabric ellenőrzi, hogy minden kifogástalan állapotát. Ha egy entitás nem megfelelő állapotú, a konfigurált házirendek használatával értékeli ki, a frissítés érvényes meghatározni a következő művelet frissítés-specifikus szabályzatokat. A frissítés (például kompatibilitását hibaállapotokat, vagy a házirendek módosítása) a felhasználói beavatkozás engedélyezése, előfordulhat, hogy szüneteltethető, vagy azt automatikusan visszaállhatnak a korábbi helyes verzióra.

Során egy *fürt* frissítési, megtekintheti a fürt frissítési állapotát. A frissítés állapota nem megfelelő értékelések, mutasson a Mi az a fürt állapota nem tartalmaz. Ha a frissítés állapotbeli problémák miatt vissza lesz állítva, a frissítési állapot megjegyzi a legutóbbi sérült okok miatt. Ezek az információk segítségével a rendszergazdák megvizsgálhatja, mi történt, miután a frissítés visszaállításra, vagy leállt.

Ehhez hasonlóan során egy *alkalmazás* frissítési, minden nem megfelelő értékelések tartalmazza az alkalmazás frissítési állapotát.

A következő alkalmazás frissítési állapotát jeleníti meg egy módosított fabric: / WordCount alkalmazás. A figyelő hibát jelzett a a replikára. A frissítés vissza, mert az állapot-ellenőrzések nem tartják tiszteletben visszaállítása folyamatban van.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Tudjon meg többet a [Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Egészségügyi értékelések használata a hibaelhárításhoz
Minden alkalommal, amikor a fürt és az alkalmazásokkal probléma van, tekintse meg a fürt vagy az alkalmazás állapota a rögzítési ponthoz, mi okozza. A nem megfelelő értékelések adja meg, hogy mi váltotta ki a jelenlegi sérült állapot adatait. Ha szeretné, részletezhet az alapvető okának azonosításához sérült gyermek entitásokkal.

Például vegyünk egy alkalmazást nem megfelelő állapotú, mert az egyik a replikák hibajelentés. A következő Powershell-parancsmagot a nem megfelelő értékelések mutatja be:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.
                                  
                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.
                                  
                                            Error event: SourceId='MyWatchdog', Property='Memory'.
                                  
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Tekintse meg a replikát annak részletes tájékoztatást:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> A nem megfelelő értékelések, akkor az első ilyen ok az entitás kiértékelése történik az aktuális állapot megjelenítése Előfordulhat, hogy több más kiváltó események ebben az állapotban, de azokat nem lehet megjelennek az értékeléseket. További információért, részletesebb adatainak megjelenítése az egészségügyi entitásokat, döntse el, a fürt összes nem megfelelő állapotú jelentést.
>
>

## <a name="next-steps"></a>További lépések
[Rendszerállapot-jelentések használata a hibaelhárítás során](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Egyéni Service Fabric-állapotjelentések hozzáadása](service-fabric-report-health.md)

[Hogyan lehet szolgáltatási állapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[A szolgáltatások helyi monitorozása és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)
