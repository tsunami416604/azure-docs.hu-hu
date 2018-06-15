---
title: Azure Service Fabric entitások megtekintése összesített állapotát |} Microsoft Docs
description: Ismerteti, hogyan lehet lekérdezni, megtekintése és Azure Service Fabric entitások összesített állapotát, állapotlekérdezések és általános lekérdezések kiértékelése.
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
ms.openlocfilehash: 2e5d1045edbbc3c71cb0ccff34d2ba327a98a409
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211857"
---
# <a name="view-service-fabric-health-reports"></a>A Service Fabric rendszerállapot-jelentések megtekintése
Az Azure Service Fabric vezet be a [állapotmodell](service-fabric-health-introduction.md) figyelés az állapotfigyelő entitások mely rendszerösszetevők és watchdogs a hajthatja végre helyi feltételek, amelyek jelentés. A [a health Store adatbázisban](service-fabric-health-introduction.md#health-store) összesíti az összes állapotadatok kifogástalan állapotú entitások megállapításához.

A fürt automatikusan töltődik állapotjelentéseket a rendszer-összetevők által küldött. További információ: [rendszerállapot-jelentések használva háríthatja](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Az entitások összesített állapotának eléréséhez több lehetőséget biztosít a Service Fabric:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vagy más képi megjelenítés eszközök
* Állapotlekérdezések száma (a PowerShell, API vagy REST)
* Általános lekérdezi az állapotfigyelő egyik (a PowerShell, API vagy REST) tulajdonságaként rendelkező entitásokat, amely visszatérési listája

Bemutatásához ezeket a beállításokat, most egy helyi fürt használatára öt csomóponttal rendelkező és a [fabric: / WordCount alkalmazás](http://aka.ms/servicefabric-wordcountapp). A **fabric: / WordCount** két alapértelmezett szolgáltatások, a következő típusú állapot-nyilvántartó szolgáltatást tartalmazó alkalmazás `WordCountServiceType`, és egy állapotmentes szolgáltatások típusú `WordCountWebServiceType`. Módosítottam a `ApplicationManifest.xml` hét cél-replikák beállítása az állapotalapú szolgáltatásból, és egy partíció szükséges. Mivel a fürt csak az öt csomóponttal, a rendszer összetevőit jelentést egy figyelmeztetés a szolgáltatás partíció mert kevesebb a cél.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>A Service Fabric Explorerben állapota
A fürt vizuális áttekintést nyújt a Service Fabric Explorerben talál. Az alábbi képen is látható, amely:

* Az alkalmazás **fabric: / WordCount** piros (a hiba), mert egy hibaesemény által jelentett **MyWatchdog** tulajdonság **rendelkezésre állási**.
* A szolgáltatások egyik **fabric: / WordCount/WordCountService** sárga (figyelmeztetés) a. A szolgáltatás úgy van beállítva, a hét replikákat, és a fürt az öt csomóponttal rendelkezik, így a két repicas nem helyezhető el. Bár ez nem látható itt, a szolgáltatás partíciója sárga miatt a rendszer jelentése `System.FM` arról, hogy `Partition is below target replica or instance count`. A sárga partíció elindítja a sárga szolgáltatás.
* A fürt miatt a piros alkalmazás piros.

A kiértékelés alapértelmezett házirendeket a fürtjegyzékben, illetve az alkalmazás jegyzékfájlja használ. Szigorú házirendek, és nem legyenek tűrni minden hiba.

A fürt a Service Fabric Explorerrel megtekintése:

![A fürt a Service Fabric Explorerrel ábrázolása.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Tudjon meg többet az [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Állapotlekérdezések száma
A Service Fabric állapotlekérdezések mutatja az egyes a támogatott [entitástípusok](service-fabric-health-introduction.md#health-entities-and-hierarchy). Az API-k, a módszerekkel hozzáférhetők [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell-parancsmagok és a többi. Ezeket a lekérdezéseket információval teljes állapotát figyelhesse az entitás: a összesített állapotát, entitás állapotával kapcsolatos események, gyermek állapotokat (ha alkalmazható), a nem megfelelő értékelések (ha az entitás állapota nem kifogástalan) és gyermekek állapotstatisztika (ha alkalmazható).

> [!NOTE]
> Teljes mértékben a telepítéskor a health Store adatbázisban egy egészségügyi entitás érték érkezett vissza. Az entitás aktív (nem törölte a) és egy jelentést. A szülő entitások a hierarchia lánc rendszer jelentések is rendelkeznie kell. Ha ezek a feltételek nem teljesülnek, a health lekérdezi térjen vissza a [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) rendelkező [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` , amely jeleníti meg, miért nem adott vissza az entitás.
>
>

A állapotlekérdezések meg kell felelnie az entitás azonosító, amely attól függ, entity Type típusként. A lekérdezések választható állapotfigyelő házirend paraméterek fogadja el. Ha nincsenek házirendek meg van adva, a [állapotházirendeket](service-fabric-health-introduction.md#health-policies) a fürt vagy az alkalmazás-jegyzékfájlból használt értékelésre. Ha a jegyzékfájlban nem tartalmazzák a következő definícióját: állapotházirendeket, az alapértelmezett házirendek kiértékelése érvényesek. Az alapértelmezett házirendek nem működését az esetleges hibákat. A lekérdezések is fogadja el a szűrők csak részleges gyermekek vagy események – a megadott szűrők tiszteletben tartják a gazdarendszerhez visszaküldésére használatos. Egy másik szűrő lehetővé teszi, hogy a gyermekek statisztika kivételével.

> [!NOTE]
> A kimeneti alkalmazza a rendszer a kiszolgáló oldalán, így az üzenet válasz mérete csökken. Azt javasoljuk, hogy Ön a kimeneti szűrők használata a visszaküldött adatok korlátozásához, nem pedig szűrőket alkalmazhat az ügyféloldalon.
>
>

Egy entitás állapotát tartalmazza:

* Az entitás összesített állapotát. A health Store adatbázisban entitás állapotjelentések száma, a gyermek állapotokat (ha alkalmazható) és az állapotházirendeket alapján számítja ki. Tudjon meg többet az [entitás állapotának kiértékelését](service-fabric-health-introduction.md#health-evaluation).  
* Az entitás állapotát eseményeket.
* Minden gyermeknek az entitások, amelyeken gyermekek állapotának gyűjteménye. Állapotokhoz entitás azonosítók és összesített állapotát tartalmazza. Ahhoz, hogy a teljes állapotát figyelhesse az gyermek, a gyermek entitástípus vonatkozóan hívja meg a lekérdezés állapotát, és adja át az alárendelt azonosító.
* A nem megfelelő értékelések, ha az entitás nem működik megfelelően a jelentés az entitás állapotát kiváltó mutasson. Az értékelések rekurzív, az aktuális állapot kiváltó gyermekek állapotfigyelő értékelések tartalmazó. Például egy figyelő elleni replika hibát jelzett. Az alkalmazás állapotának miatt nem megfelelő állapotú szolgáltatás; a nem megfelelő próbaverzióként jeleníti meg. a szolgáltatás állapota nem megfelelő, egy partíciót hibás; miatt a partíció nem kifogástalan hibás; replika miatt a replika állapota nem megfelelő a jelentés figyelő hiba miatt.
* A gyermekek rendelkező entitások minden gyermek típusú állapotának statisztikai adatait. Például a fürt állapotának alkalmazások, szolgáltatások, partíciók, a replikákat teljes számát jeleníti meg, és a fürt entitások telepíteni. Szolgáltatás állapotát jeleníti meg a partíciók és a megadott szolgáltatás alatt futó replikák száma.

## <a name="get-cluster-health"></a>Fürt állapotának beolvasása
A fürt entitás állapotát adja vissza, és tartalmazza az alkalmazások és a csomópontok (a fürt gyermekeinek) állapotokhoz. Bemenet:

* [Választható] A fürt állapotházirend annak ellenőrzésére használ a csomópontokon és a fürthöz kapcsolódó események.
* [Választható] Az alkalmazás állapotfigyelő házirend-hozzárendelés, felül az alkalmazás-jegyzékfájl házirendeket rendszerállapot-szabályzatokkal.
* [Választható] Események, a csomópontok és az alkalmazásokat, amelyek adja meg, mely tételek szűrők iránt, és vissza kell adni az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Események, csomópontokat és alkalmazások segítségével állapotellenőrzése entitáshoz összesítve, függetlenül a szűrőt.
* [Választható] Szűrő állapotstatisztika kizárása.
* [Választható] Szűrőt, amely tartalmazza a fabric: / System egészségügyi statisztikák a rendszerállapot-statisztika. Csak érvényes a állapotstatisztika nem dimenziónevek kizárásakor. Alapértelmezés szerint a állapotstatisztika csak felhasználói alkalmazások és a nem a rendszer alkalmazás statisztikák tartalmazza.

### <a name="api"></a>API
Ahhoz, hogy a fürt állapotát, hozzon létre egy `FabricClient` , és hívja meg a [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metódust a **HealthManager**.

A következő hívást lekérdezi a fürt állapota:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

A következő kódot a fürt állapota lekérdezi a csomópontok és alkalmazások egyéni fürt állapotházirend és a szűrők használatával. Meghatározza, hogy a health statisztika tartalmazza-e a fabric: / System statisztika. Létrehozza [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), amely a bemeneti adatokat tartalmazza.

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
A parancsmag a fürt állapotának eléréséhez [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag.

A fürt állapota az öt csomóponttal, a rendszer az alkalmazás és fabric: / WordCount leírtak szerint konfigurálva.

A következő parancsmag lekéri a fürt állapotfigyelő alapértelmezett házirendek használatával. Az összesített állapota figyelmet igényel, mert a fabric: / WordCount alkalmazás figyelmeztetés is van. Vegye figyelembe, hogy a nem megfelelő értékelések hogyan részletekkel szolgálnak a feltételeket, amelyek indított összesített állapotát.

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

A következő PowerShell-parancsmag a fürt állapotának lekérése egy egyéni alkalmazás-házirend használatával. Csak az alkalmazások és a hiba vagy figyelmeztetés csomópontjának eredményeket szűri. Ennek eredményeképpen a csomópontok nem ad vissza, mivel ezek az összes kifogástalan. Csak a fabric: / WordCount alkalmazás tiszteletben tartja az alkalmazások szűrőt. Mivel az egyéni házirend határozza meg, figyelembe kell venni a háló figyelmeztetés hibaként: / WordCount alkalmazás, az alkalmazás hasonlóan hiba történik, és így az a fürt.

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
Kaphat a fürt health használata az egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) vagy egy [POST kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-node-health"></a>Csomópont állapotának beolvasása
Egy csomópont entitás állapotát adja vissza, és a állapotával kapcsolatos események küldött, a csomópont tartalmazza. Bemenet:

* [Szükséges] A csomópont neve, amely azonosítja a csomóponton.
* [Választható] A fürt állapotának szabályzatbeállítások állapotának értékeléséhez használt.
* [Választható] Adja meg, mely tételek események szűrők iránt, és vissza kell adni az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Az összevont entitásállapota, függetlenül a szűrő értékeléséhez használt összes eseményt.

### <a name="api"></a>API
Ahhoz, hogy a csomópont állapotát az API-n keresztül, hozzon létre egy `FabricClient` , és hívja meg a [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) a HealthManager metódust.

A következő kód jogosultságot kap a csomópont állapotát a megadott csomópont nevét:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

A következő kódot a csomópont állapotát lekérdezi a megadott csomópont neve, és továbbadja eseményszűrő és egyéni házirend használatával [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A parancsmagot, hogy megkapja a csomópont állapotát van [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag.
A következő parancsmag lekéri a csomópont állapotát alapértelmezett házirendek használatával:

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

A következő parancsmag lekéri az összes csomópont állapotát a fürt:

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
Kaphat a csomópont állapotát egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) vagy egy [POST kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-application-health"></a>Alkalmazás állapotának beolvasása
Egy alkalmazás entitás állapotának beolvasása. A telepített alkalmazás és szolgáltatás gyermekek állapotokhoz tartalmaz. Bemenet:

* [Szükséges] Az alkalmazás neve (URI), amely azonosítja az alkalmazást.
* [Választható] Az alkalmazás állapotházirendje felül az alkalmazás-jegyzékfájl házirendeket.
* [Választható] Események, szolgáltatások, és adja meg, mely tételek üzembe helyezett alkalmazások szűrők érdeklő, és vissza kell adni az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Események, szolgáltatások és telepített alkalmazások segítségével állapotellenőrzése entitáshoz összesítve, függetlenül a szűrőt.
* [Választható] Szűrő, az egészségügyi statisztikák kizárása. Ha nincs megadva, az egészségügyi statisztikák közé tartozik a ok, figyelmeztetés és hibák száma az összes alkalmazás számára: szolgáltatások, partíciók, replikák alkalmazások telepítését, és központilag telepített szervizcsomagok.

### <a name="api"></a>API
Ahhoz, hogy az alkalmazás állapotának, hozzon létre egy `FabricClient` , és hívja meg a [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) a HealthManager metódust.

A következő kód jogosultságot kap az alkalmazásnév (URI) alkalmazás állapotát:

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Az alábbi kód lekérdezi az alkalmazás állapotát az alkalmazásnév (URI), szűrők és a megadott egyéni házirendek [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
A parancsmagot, hogy megkapja az alkalmazás állapotának van [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag.

A következő parancsmag visszaadja állapotát a **fabric: / WordCount** alkalmazás:

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

A következő PowerShell-parancsmagot az egyéni házirendek továbbítja. Szűrők is gyermekek és események.

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
Beszerezheti az alkalmazás állapotának egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) vagy egy [POST kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-service-health"></a>Szolgáltatás állapotának beolvasása
Az állapotfigyelő szolgáltatás entitás adja vissza. A partíció állapotokat tartalmaz. Bemenet:

* [Szükséges] A szolgáltatás neve (URI), amely azonosítja a szolgáltatást.
* [Választható] Az alkalmazás állapotházirendje felül az alkalmazás jegyzékének házirend.
* [Választható] Az események és adja meg, mely tételek partícióknak szűrők érdeklő, és vissza kell adni az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Események és a partíciók állapotellenőrzése entitáshoz összesítve, függetlenül a szűrő szolgálnak.
* [Választható] Szűrő állapotstatisztika kizárása. Ha nincs megadva, az egészségügyi statisztika megjelenítése a ok, figyelmeztetés, és hiba számára az összes partíciót és a szolgáltatás replikák száma.

### <a name="api"></a>API
Ahhoz, hogy a szolgáltatás állapotát az API-n keresztül, hozzon létre egy `FabricClient` , és hívja meg a [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) a HealthManager metódust.

Az alábbi példa lekérdezi a megadott szolgáltatásnév (URI) egy szolgáltatásának állapotát:

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

A következő kód jogosultságot kap a szolgáltatás állapotát a megadott szolgáltatásnév (URI), a szűrők és egyéb egyéni házirend használatával [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A parancsmagot, hogy megkapja a szolgáltatás állapotát az [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag.

A következő parancsmag lekéri a szolgáltatás állapotát alapértelmezett házirendek használatával:

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
Beszerezheti a szolgáltatás állapotát egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) vagy egy [POST-kérelmet](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-partition-health"></a>Partíció állapotának beolvasása
Egy partíció entitás állapotának beolvasása. A replika állapotokat tartalmaz. Bemenet:

* [Szükséges] A partíció Azonosítót (GUID), amely azonosítja a partíciót.
* [Választható] Az alkalmazás állapotházirendje felül az alkalmazás jegyzékének házirend.
* [Választható] Az események és adja meg, mely tételek replikák szűrők érdeklő, és vissza kell adni az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Események és a replikák állapotellenőrzése entitáshoz összesítve, függetlenül a szűrő szolgálnak.
* [Választható] Szűrő állapotstatisztika kizárása. Ha nincs megadva, az egészségügyi statisztikák vannak hány replikák ok, figyelmeztetés és hiba állapotok.

### <a name="api"></a>API
Ahhoz, hogy a partíció állapotát az API-n keresztül, hozzon létre egy `FabricClient` , és hívja meg a [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) a HealthManager metódust. Adja meg a választható paraméterek:, hozzon létre [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
A parancsmagot, hogy megkapja a partíció állapotfigyelő van [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag.

A következő parancsmag lekéri az összes partíció állapotát a **fabric: / WordCount/WordCountService** szolgáltatás és a szűrők replika állapotokat ki:

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
Kaphat a partíció health használata az egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) vagy egy [POST kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-replica-health"></a>A replika állapotának beolvasása
Egy állapotalapú szolgáltatási replika- vagy egy állapotmentes szolgáltatások állapotának beolvasása. Bemenet:

* [Szükséges] A partíció Azonosítót (GUID) és a replika azonosítója, amely azonosítja a replikát.
* [Választható] Az alkalmazás állapotának házirend paraméterei felül az alkalmazás-jegyzékfájl házirendeket.
* [Választható] Adja meg, mely tételek események szűrők iránt, és vissza kell adni az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Az összevont entitásállapota, függetlenül a szűrő értékeléséhez használt összes eseményt.

### <a name="api"></a>API
Ahhoz, hogy a replika állapota az API-n keresztül, hozzon létre egy `FabricClient` , és hívja meg a [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) a HealthManager metódust. Speciális paraméterek megadásához használja [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
A parancsmagot, hogy megkapja a replika állapota az [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag.

A következő parancsmag lekéri a szolgáltatás összes partíciók az elsődleges másodpéldány állapotát:

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
Kaphat a replika health használata az egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) vagy egy [POST kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-deployed-application-health"></a>Telepített alkalmazás állapotának beolvasása
Egy csomópont entitás központi telepítésű alkalmazás állapotának beolvasása. A telepített service-csomag állapotokat tartalmaz. Bemenet:

* [Szükséges] Az alkalmazás neve (URI) és a csomópont neve (karakterlánc) azonosító a telepített alkalmazás.
* [Választható] Az alkalmazás állapotházirendje felül az alkalmazás-jegyzékfájl házirendeket.
* [Választható] Az események és adja meg, mely tételek telepített szervizcsomagok szűrők érdeklő, és vissza kell adni az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Események és a telepített szervizcsomagok állapotellenőrzése entitáshoz összesítve, függetlenül a szűrő szolgálnak.
* [Választható] Szűrő állapotstatisztika kizárása. Ha nincs megadva, az egészségügyi statisztikák telepített szervizcsomagok száma ok, figyelmeztetés és hiba állapotának megjelenítése.

### <a name="api"></a>API
Ahhoz, hogy az API-n keresztül csomóponton központi telepítésű alkalmazás állapotát, hozzon létre egy `FabricClient` , és hívja meg a [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) a HealthManager metódust. Adja meg a választható paraméterek: [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
A parancsmag a központilag telepített alkalmazás állapotának eléréséhez [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag. Szeretné tudni, ha egy alkalmazás lett telepítve, futtassa [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) , és tekintse meg a központilag telepített alkalmazás gyermekei.

A következő parancsmag lekéri a állapotát a **fabric: / WordCount** a központilag telepített alkalmazás **_Node_2**.

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
Kaphat a telepített alkalmazás health használata az egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) vagy egy [POST kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="get-deployed-service-package-health"></a>Telepített szolgáltatások csomag állapotának beolvasása
Egy telepített szolgáltatást csomag entitás állapotának beolvasása. Bemenet:

* [Szükséges] Az alkalmazásnév (URI), a csomópont neve (karakterlánc) és a service manifest, amelyek azonosítják a telepített service-csomag neve (karakterlánc).
* [Választható] Az alkalmazás állapotházirendje felül az alkalmazás jegyzékének házirend.
* [Választható] Adja meg, mely tételek események szűrők iránt, és vissza kell adni az eredmény (például csak, a hibák vagy figyelmeztetések és hibák). Az összevont entitásállapota, függetlenül a szűrő értékeléséhez használt összes eseményt.

### <a name="api"></a>API
Ahhoz, hogy a telepített szervizcsomag állapotát az API-n keresztül, hozzon létre egy `FabricClient` , és hívja meg a [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) a HealthManager metódust. Adja meg a választható paraméterek: [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
A parancsmagot, hogy megkapja a telepített szolgáltatások csomag állapotfigyelő van [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag. Ha egy alkalmazás központi telepítése megtekintéséhez futtassa [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) , és tekintse meg a központilag telepített alkalmazások. Melyik szolgáltatás csomagok szerepelnek az alkalmazás megtekintéséhez tekintse meg a központilag telepített szolgáltatással csomag gyermekek a [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) kimeneti.

A következő parancsmag lekéri a állapotát a **WordCountServicePkg** a szolgáltatáscsomagot a **fabric: / WordCount** a központilag telepített alkalmazás **_Node_2**. Az entitásnak van **System.Hosting** sikeres service-csomag és a belépési pont aktiválása és sikeres szolgáltatástípus regisztráció a jelentésekre.

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
Kaphat a telepített szolgáltatások csomag health használata az egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) vagy egy [POST kérelem](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) , amely tartalmazza a szervezet ismertetett állapotházirendeket.

## <a name="health-chunk-queries"></a>Adatrészlet állapotlekérdezések száma
Az adattömbök állapotlekérdezések több szintű fürt gyermekek (rekurzív), egy bemeneti szűrőket adhat vissza. Támogatja a speciális szűrők, amelyek lehetővé teszik a magas fokú rugalmasságot biztosít a visszaadandó gyermekek kiválasztása. A szűrők gyermekek adhatja meg az egyedi azonosító, vagy más csoportazonosítókhoz és/vagy állapotokat. Alapértelmezés szerint nincsenek gyermekei tartoznak, első szintű gyermekek mindig tartalmazó állapotfigyelő parancsokat szemben.

A [állapotlekérdezések](service-fabric-view-entities-aggregated-health.md#health-queries) szükséges szűrők száma a megadott entitás csak az első szintű gyermekeit adja vissza. Ahhoz, hogy a gyermekek gyermekei, meg kell hívnia további rendszerállapot API-k minden egyes entitásnál iránt. Ehhez hasonlóan ahhoz, hogy az adott entitások állapotát, meg kell hívni egy rendszerállapot API minden kívánt entitás. A speciális szűrési adatrészlet lekérdezés lehetővé teszi több elem egyik fontos egy lekérdezést, minimalizálja az üzenet mérete és üzenetek száma.

Az adattömbök lekérdezés értéke, hogy kaphat állapota további fürt entitásokat (vélhetően fürt entitásokhoz kezdve a kötelező gyökérszintű) egy hívásban. Összetett állapot lekérdezés is express, mint:

* Visszatérési csak alkalmazások hiba, és ezeket az alkalmazásokat tartalmaznak minden figyelmeztetés vagy hiba. A kiadott szolgáltatások tartalmazza az összes partíció.
* Térjen vissza a csak a megadott névvel négy futó alkalmazások állapotát.
* Csak a kívánt alkalmazáshoz típusú alkalmazások állapotának visszaadása.
* Térjen vissza az összes üzembe helyezett entitások csomóponton. Visszaadja az összes alkalmazásra, az összes telepített alkalmazások a megadott csomópont és a telepített service-csomagok ezen a csomóponton.
* Hiba található összes replika visszaadása. Alkalmazások, szolgáltatások, partíciók és csak a replikák hiba adja vissza.
* Térjen vissza az összes alkalmazást. Az egy megadott szolgáltatás tartalmazza az összes partíció.

A health adatrészlet lekérdezés jelenleg fel van fedve, csak a fürt entitásnál. Azt jelzi, hogy a fürt állapotfigyelő adattömb, amely tartalmazza:

* A fürt állapotát összesíti.
* A rendszerállapot állapot adatrészlet csomópontlista, figyelembe vegyék bemeneti szűrők.
* Állapotfigyelő állapot adatrészlet alkalmazások listáját, amely a bemeneti szűrők tiszteletben. Egyes alkalmazás állapotának állapot adattömbök adatrészlet listáját tartalmazza, minden szolgáltatások tiszteletben bemeneti szűrők és adatrészlet tiszteletben tartják a szűrők összes üzembe helyezett alkalmazások listáját. Ugyanaz a szolgáltatások és telepített alkalmazások számára. Ezzel a módszerrel a fürt összes entitásának potenciálisan visszaadható. Ha kért hierarchikus.

### <a name="cluster-health-chunk-query"></a>Fürt állapotfigyelő adatrészlet lekérdezés
A fürt entitás állapotát adja vissza, és a hierarchikus egészségügyi állapot adattömbök szükséges gyermekek tartalmazza. Bemenet:

* [Választható] A fürt állapotházirend annak ellenőrzésére használ a csomópontokon és a fürthöz kapcsolódó események.
* [Választható] Az alkalmazás állapotfigyelő házirend-hozzárendelés, felül az alkalmazás-jegyzékfájl házirendeket rendszerállapot-szabályzatokkal.
* [Választható] Csomópont-és alkalmazásokat, amelyek adja meg, mely tételek érdeklő, és vissza kell adni az eredményben. A szűrők jellemző entitások entitás vagy csoporthoz, vagy azon a szinten az összes entitás vonatkoznak. A szűrők listáját egy általános szűrő és/vagy a lekérdezés által visszaadott részletes entitások adott készlet szűrők tartalmazhat. Ha üres, a gyermekek nem lehet megjeleníteni alapértelmezés szerint.
  További információk a szűrők [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) és [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Az alkalmazás szűrők is rekurzív módon speciális szűrők megadása a gyermekek számára.

Az adattömbök eredmény tartalmazza az alárendelt tiszteletben tartják a szűrők.

Jelenleg a adatrészlet lekérdezés nem ad vissza a nem megfelelő értékelések vagy entitás események. További adatokat érhető el a meglévő fürt állapotfigyelő query használatával.

### <a name="api"></a>API
Ahhoz, hogy a fürt állapotának adattömbök, hozzon létre egy `FabricClient` , és hívja meg a [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metódust a **HealthManager**. Adhasson [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) leírására házirendek és a speciális szűrők.

A következő kódot a speciális szűrők fürt állapotfigyelő adatrészlet lekérdezi.

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
A parancsmag a fürt állapotának eléréséhez [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag.

A következő kód jogosultságot kap csomópontok, csak akkor, ha egy konkrét csomóponton, amely mindig vissza kell adni az kivételével hiba vannak.

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

A következő parancsmag lekéri a fürt adattömbök alkalmazás szűrők.

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

A következő parancsmagot a csomópont minden telepített entitásokat ad vissza.

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
Kaphat a fürt állapotának rendszer egy [GET kérés](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) vagy egy [POST kérelem](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) , amely tartalmazza a házirendek és a szervezet leírt speciális szűrők.

## <a name="general-queries"></a>Általános lekérdezések
Általános lekérdezésekben Service Fabric entitások, megadott típusú listáját adja vissza. Azok ki vannak téve az API-n keresztül (a metódusai keresztül **FabricClient.QueryManager**), PowerShell-parancsmagok és a többi. Ezeket a lekérdezéseket a segédlekérdezések több összetevőkből összesíteni. Egyik azokat a [a health Store adatbázisban](service-fabric-health-introduction.md#health-store), amely feltölti az összesített állapota minden egyes lekérdezés eredménye.  

> [!NOTE]
> Általános lekérdezések összesített állapotát, az entitást adja vissza, és nem tartalmaz gazdag egészségügyi adatokat. Entitás állapota nem kifogástalan, ha nyomon lehet követni az állapotlekérdezések lekérni a egészségügyi adatok, beleértve az események, a gyermek állapotokat és a nem megfelelő értékelések.
>
>

Általános lekérdezések vissza egy entitás ismeretlen állapot, akkor lehetséges, hogy a health Store adatbázisban nem rendelkezik teljes körű adatok entitás. Akkor is, hogy a health Store adatbázisban a segédlekérdezés nem volt sikeres (például kommunikációs hiba történt, vagy a health Store adatbázisban halmozódni volt). Az entitás állapotát lekérdezés nyomon követéséhez. A segédlekérdezés hibát jelzett a átmeneti, például a hálózati problémák, a követési lekérdezés esetleg sikeresek lehetnek. Azt is adhat meg további részleteket a kapcsolatos miért nincs felfedve, az entitás a health Store adatbázisból.

A lekérdezések **HealthState** a szervezetek a következők:

* Csomópont lista: a lista csomópontokat a fürtben (lapozható) adja vissza.
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Alkalmazáslista: a fürt (lapozható) az alkalmazások listáját adja vissza.
  * Alkalmazásprogramozási felületek: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista: egy alkalmazás (lapozható) a szolgáltatások listáját adja vissza.
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partition listában: partícióinak listáját adja vissza (lapozható) szolgáltatás.
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Listáján: egy partíció (lapozható) a replikák listáját adja vissza.
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Telepített alkalmazások listája: a csomópont a központilag telepített alkalmazások listáját adja vissza.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Telepített szolgáltatás csomaglista: a központilag telepített alkalmazás service-csomagok listáját adja vissza.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> A lekérdezések némelyikének térjen vissza a lapozható eredmények. Ezeket a lekérdezéseket visszatérési származtatva listáját [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Az eredmények nem férnek el egy üzenetet, ha csak egy lap ad vissza, és egy ContinuationToken, amely nyomon követi, ahol számbavételi leállt. Továbbra is ugyanabban a lekérdezésben és folytatási adhatók át az előző lekérdezés következő lekéréséhez.
>
>

### <a name="examples"></a>Példák
A következő kódot a nem megfelelő alkalmazások lekérdezi a fürt:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

A következő parancsmag lekéri az alkalmazás részletes adatainak a fabric: / WordCount alkalmazás. Láthatja, hogy állapot figyelmeztetés.

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

A következő parancsmag lekéri a szolgáltatások egy hiba állapota:

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
A fürt és az alkalmazás egy figyelt frissítés során a Service Fabric győződjön meg arról, hogy minden továbbra is kifogástalan állapotát ellenőrzi. Entitás állapota nem megfelelő, mint a konfigurált házirendek kiértékelése, ha a frissítés alkalmazva frissítés-specifikus házirendjében tekintse meg a következő műveletet. A frissítés szünetel előfordulhat, hogy engedélyezi a felhasználói beavatkozást (például a hiba kijavítása, vagy házirendek módosítása), vagy az lehet, hogy automatikusan állítsa vissza az előző jó verziójára.

Során egy *fürt* frissítését, hogy megkaphassa a fürt frissítési állapot. A frissítési állapot nem kifogástalan értékelések, mutasson a mi állapota nem megfelelő a fürt tartalmaz. Ha a frissítés állapotának, problémákból adódó vissza lesz állítva, a frissítési állapot megjegyzi az utolsó sérült okokból. Az információk segítségével a rendszergazdák kivizsgálni, mi a probléma után a frissítés visszaállításra, vagy leállt.

Ehhez hasonlóan során egy *alkalmazás* frissítés, bármely sérült értékelések tartalmazza az alkalmazás frissítési állapotát.

A következő alkalmazás frissítési állapotát jeleníti meg a módosított háló: / WordCount alkalmazás. A figyelő hibát jelzett az egyik a replikáit. A frissítés folyamatban van, mert az állapotfigyelő ellenőrzések nem veszik.

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

További információ a [Service Fabric az alkalmazásfrissítés](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Állapotfigyelő értékelések használva háríthatja
Ha a fürt vagy az alkalmazás problémát, tekintse meg a rögzítési ponthoz, mi fürt vagy az alkalmazás állapotát. A nem megfelelő állapotú értékelést az aktuális nem megfelelő állapot kiváltó okáról részleteit tartalmazzák. Ha szeretné, részletezhető le nem megfelelő állapotú gyermekfigyelőket entitások azonosítására az alapvető okát.

Vegye figyelembe például az alkalmazás nem megfelelő, mert a replikáit egyik egy esetleges hibajelentésben való megjelenítéshez. A következő Powershell-parancsmagot a nem megfelelő értékelések jeleníti meg:

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

A replika további információkat is megtekinthetők:

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
> A nem megfelelő értékelések megjelenítése, az első ilyen ok az entitás ki lesz értékelve az aktuális állapotát. Előfordulhat, hogy több más kiváltó események ebben az állapotban, de azokat nem kell megjelennek az értékelést. További információért részletekbe menően tárhatják fel mérje fel, a fürt összes nem megfelelő jelentés állapotfigyelő entitásokat.
>
>

## <a name="next-steps"></a>További lépések
[Rendszerállapot-jelentések használata a hibaelhárítás során](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adja hozzá az egyéni Service Fabric állapotjelentések száma](service-fabric-report-health.md)

[Jelentés és a szolgáltatás állapotának ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Figyelése és diagnosztizálása helyileg szolgáltatások](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)
