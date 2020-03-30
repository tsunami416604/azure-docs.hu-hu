---
title: Az Azure Service Fabric-entitások összesített állapotának megtekintése
description: Bemutatja, hogyan lehet lekérdezni, megtekinteni és kiértékelni az Azure Service Fabric-entitások összesített állapotát az állapotlekérdezések és az általános lekérdezések segítségével.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d02d8f717801bf51e43c9dafa5eb9379d0737674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464126"
---
# <a name="view-service-fabric-health-reports"></a>A Service Fabric állapotjelentésének megtekintése
Az Azure Service Fabric egy [állapotmodellt](service-fabric-health-introduction.md) vezet be az állapotentitások, amelyeken a rendszer-összetevők és a figyelők jelenthetik a helyi feltételeket, amelyeket figyelnek. Az [állapottároló](service-fabric-health-introduction.md#health-store) összesíti az összes állapotadatot annak meghatározásához, hogy az entitások kifogástalanállapotúak-e.

A fürt automatikusan feltöltődik a rendszerösszetevők által küldött állapotjelentésekkel. További [információ: A rendszerállapot-jelentések használata a hibaelhárításhoz.](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

A Service Fabric többféle lehetőséget biztosít az entitások összesített állapotának lejáratára:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vagy más vizualizációs eszközök
* Állapotlekérdezések (a PowerShell, API vagy REST)
* Általános lekérdezések, amelyek az egyik tulajdonságként állapotú entitások listáját adják vissza (a PowerShellen, az API-n vagy a REST-en keresztül)

Ezeknek a beállításoknak a bemutatásához használjon egy helyi fürtöt öt csomóponttal és a [fabric:/WordCount alkalmazást.](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg) A **fabric:/WordCount** alkalmazás két alapértelmezett szolgáltatást tartalmaz, `WordCountServiceType`egy állapotalapú szolgáltatást `WordCountWebServiceType`és egy állapotnélküli típusú szolgáltatást. Megváltoztattam a `ApplicationManifest.xml` hét cél replikák az állapotalapú szolgáltatás és egy partíció. Mivel a fürtben csak öt csomópont található, a rendszerösszetevők figyelmeztetést jelentenek a szolgáltatáspartíción, mert az a célszám alatt van.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Állapot a Service Fabric Intézőben
A Service Fabric Explorer vizuális képet nyújt a fürtről. Az alábbi képen látható, hogy:

* Az alkalmazás **fabric:/WordCount** piros (hiba), mert van egy hiba esemény által jelentett **MyWatchdog** a tulajdonság **rendelkezésre állása**.
* Egyik szolgáltatása, **a fabric:/WordCount/WordCountService** sárga (figyelmeztetésben). A szolgáltatás hét replikával van konfigurálva, és a fürt öt csomópontossal rendelkezik, így két replikák nem helyezhető el. Bár itt nem jelenik meg, a szolgáltatáspartíció sárga, `System.FM` mert `Partition is below target replica or instance count`a rendszerjelentése azt mondja, hogy . A sárga partíció elindítja a sárga szolgáltatást.
* A fürt piros, mert a piros alkalmazás.

A kiértékelés a fürtjegyzék és az alkalmazásjegyzék alapértelmezett házirendjeit használja. Ezek szigorú politikák, és nem tolerálják a kudarcokat.

A fürt nézete a Service Fabric Explorer segítségével:

![A fürt nézete a Service Fabric Explorer segítségével.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> További információ a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)ről.
>
>

## <a name="health-queries"></a>Egészségügyi lekérdezések
A Service Fabric minden egyes támogatott [entitástípushoz](service-fabric-health-introduction.md#health-entities-and-hierarchy)elérhetővé teszi az állapotlekérdezéseket. Az API-n keresztül érhetők el a [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), a PowerShell-parancsmagok és a REST metódusai használatával. Ezek a lekérdezések teljes körű állapotinformációt adnak vissza az entitásról: az összesített állapot, az entitás állapotának állapota, a gyermek állapotállapota (ha van), a nem megfelelő állapotú értékelések (ha az entitás nem kifogástalan), és a gyermekek állapotstatisztikái (amikor alkalmazandó).

> [!NOTE]
> Egy állapotentitás tért vissza, ha teljesen ki van töltve a health Store.A health entity is returned when it is fully populated in the health store. Az entitásnak aktívnak (nem töröltnek) kell lennie, és rendszerjelentéssel kell rendelkeznie. A hierarchialáncban lévő szülőentitásainak rendszerjelentésekkel is rendelkezniük kell. Ha a feltételek bármelyike nem teljesül, az állapotlekérdezések egy [FabricException fabricErrorCode-mal](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` adnak vissza, amely megmutatja, hogy miért nem adja vissza az entitást. [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)
>
>

Az állapotlekérdezéseknek át kell adniuk az entitásazonosítót, amely az entitás típusától függ. A lekérdezések nem kötelező állapotházirend-paramétereket fogadnak el. Ha nincs megadva állapotházirend, a fürt vagy az alkalmazásjegyzék [állapotházirendjei](service-fabric-health-introduction.md#health-policies) kiértékelésre szolgálnak. Ha a jegyzékek nem tartalmaznak definíciót az állapotházirendek, az alapértelmezett állapotházirendek kiértékelésre használják. Az alapértelmezett állapotházirendek nem tolerálják a hibákat. A lekérdezések is elfogadja szűrők et vissza csak részleges gyermekek vagy események - az is, hogy tartsák tiszteletben a megadott szűrőket. Egy másik szűrő lehetővé teszi a gyermekek statisztikáinak kizárását.

> [!NOTE]
> A kimeneti szűrők a kiszolgálóoldalon vannak alkalmazva, így az üzenet válaszmérete csökken. Azt javasoljuk, hogy használja a kimeneti szűrőket a visszaadott adatok korlátozására, ahelyett, hogy szűrőket alkalmazna az ügyféloldalon.
>
>

Egy entitás állapota a következőket tartalmazza:

* Az entitás összesített állapota. Az állapottároló az entitás állapotjelentései, a gyermekállapot-állapotok (adott esetben) és az állapotházirendek alapján számítja ki. További információ az [entitásállapot-kiértékelésről.](service-fabric-health-introduction.md#health-evaluation)  
* Az entitás állapoteseményei.
* A gyűjtemény az összes gyermek állapotok az entitások, amelyek a gyermekek. Az állapotok entitásazonosítókat és az összesített állapotot tartalmaznak. A gyermek teljes állapotának lehívásához hívja meg a gyermekentitás-típus lekérdezési állapotát, és adja át a gyermekazonosítót.
* A nem megfelelő állapotú értékelések, amelyek az entitás állapotát kiváltó jelentésre mutatnak, ha az entitás nem kifogástalan. Az értékelések rekurzív, amely tartalmazza a gyermekek egészségügyi értékelések, amelyek kiváltották a jelenlegi állapot. Például egy figyelő jelentett egy hibát egy replika ellen. Az alkalmazás állapota egy nem megfelelő állapotú szolgáltatás miatt nem megfelelő állapotú kiértékelést mutat; a szolgáltatás hibás partíció miatt nem kifogástalan; a partíció hibás replikája miatt nem kifogástalan; a replika nem kifogástalan a figyelő hibaállapot-jelentése miatt.
* A gyermekkel foglalkozó entitások összes gyermektípusának állapotstatisztikái. A fürt állapota például a fürtben lévő alkalmazások, szolgáltatások, partíciók, replikák és telepített entitások teljes számát mutatja. A szolgáltatás állapota a megadott szolgáltatás alatt található partíciók és replikák teljes számát mutatja.

## <a name="get-cluster-health"></a>Fürtállapotának beszereznie
A fürtentitás állapotát adja vissza, és az alkalmazások és csomópontok (a fürt gyermekei) állapotát tartalmazza. Bemenet:

* [Nem kötelező] A csomópontok és a fürtesemények kiértékeléséhez használt fürtállapot-házirend.
* [Nem kötelező] Az alkalmazás állapotházirend-leképezése az alkalmazásjegyzék-házirendek felülbírálásához használt állapotházirendekkel.
* [Nem kötelező] Események, csomópontok és alkalmazások szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményben vissza kell adni őket (például csak hibák, vagy figyelmeztetések és hibák egyaránt). Minden esemény, csomópont és alkalmazás az entitás összesített állapotának kiértékelésére szolgál, a szűrőtől függetlenül.
* [Nem kötelező] Szűrő az állapotstatisztikák kizárásához.
* [Nem kötelező] Szűrő, amely tartalmazza a szövet:/Rendszer állapotstatisztikái az állapotstatisztikákban. Csak akkor alkalmazható, ha az egészségügyi statisztikák nincsenek kizárva. Alapértelmezés szerint az állapotstatisztika csak a felhasználói alkalmazások statisztikáit tartalmazza, a Rendszer alkalmazásra vonatkozóadatokat nem.

### <a name="api"></a>API
A fürt állapotának `FabricClient` lehívásához hozzon létre egy és hívja meg a [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metódust a **HealthManager en.**

A következő hívás lekéri a fürt állapotát:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

A következő kód lekéri a fürt állapotát egy egyéni fürt állapotházirend és a csomópontok és alkalmazások szűrőihasználatával. Azt határozza meg, hogy az állapotstatisztikák tartalmazzák a fabric:/Rendszer statisztikák. Létrehozza [a ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription)programot, amely a bemeneti adatokat tartalmazza.

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
A fürt állapotának lekért parancsmag a [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával.

A fürt állapota öt csomópont, a rendszeralkalmazás és a fabric:/WordCount a leírtak szerint konfigurálva.

A következő parancsmag leálla a fürt állapotát az alapértelmezett állapotházirendek használatával. Az összesített állapot figyelmeztetés, mert a fabric:/WordCount alkalmazás figyelmeztetés. Vegye figyelembe, hogy a nem megfelelő állapotú értékelések részletesen ismertetik az összesített állapotot kiváltó feltételeket.

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

A következő PowerShell-parancsmag leállatja a fürt állapotát egy egyéni alkalmazásházirend használatával. Szűri az eredményeket, hogy csak alkalmazásokat és csomópontokat kapjon hiba vagy figyelmeztetés esetén. Ennek eredményeképpen nem ad nak vissza csomópontokat, mivel mindegyik egészséges. Csak a fabric:/WordCount alkalmazás tiszteletben tartja az alkalmazások szűrőjét. Mivel az egyéni házirend határozza meg, hogy a figyelmeztetéseket a fabric:/WordCount alkalmazás hibáinak tekintsék, az alkalmazás kiértékelése hibás, és a fürt is.

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
A fürt állapotát [get-kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) vagy [postai kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) kaphatja meg, amely a szervezetben leírt állapotházirendeket tartalmaz.

## <a name="get-node-health"></a>A csomópont állapotának beszereznie
Egy csomópontentitás állapotát adja vissza, és a csomóponton jelentett állapoteseményeket tartalmazza. Bemenet:

* [Kötelező] A csomópontot azonosító csomópontnév.
* [Nem kötelező] Az állapot kiértékelésére használt fürtállapot-házirend-beállítások.
* [Nem kötelező] Az események szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményben vissza kell adni őket (például csak hibák, vagy figyelmeztetések és hibák egyaránt). Az összes esemény az entitás összesített állapotának kiértékelésére szolgál, függetlenül a szűrőtől.

### <a name="api"></a>API
Ha az API-n keresztül szeretné `FabricClient` lekérni a csomópont állapotát, hozzon létre egy és hívja meg a [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) metódust a HealthManager-en.

A következő kód lekéri a megadott csomópontnév csomópont-állapotát:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

A következő kód lekéri a megadott csomópontnév csomópont-állapotát, és a [NodeHealthQueryDescription-en](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription)keresztül továbbítja az események szűrőjét és az egyéni házirendet:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A csomópont állapotának leéséhez szükséges parancsmag a [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával.
A következő parancsmag leállatja a csomópont állapotát az alapértelmezett állapotházirendek használatával:

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

A következő parancsmag a fürt összes csomócsomójának állapotát kapja:

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
A csomópont állapotát [get-kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) vagy [postai kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) kaphatja meg, amely a szervezetben leírt állapotházirendeket tartalmaz.

## <a name="get-application-health"></a>Alkalmazásállapotának beszereznie
Egy alkalmazásentitás állapotát adja eredményül. Az üzembe helyezett alkalmazás és a szolgáltatás gyermekek állapotait tartalmazza. Bemenet:

* [Kötelező] Az alkalmazást azonosító alkalmazásnév (URI).
* [Nem kötelező] Az alkalmazás jegyzékfájl-házirendjének felülbírálásához használt alkalmazásállapot-házirend.
* [Nem kötelező] Események, szolgáltatások és telepített alkalmazások szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményben vissza kell adni őket (például csak hibák, vagy figyelmeztetések és hibák egyaránt). Minden esemény, szolgáltatás és üzembe helyezett alkalmazás az entitás összesített állapotának kiértékelésére szolgál, függetlenül a szűrőtől.
* [Nem kötelező] Szűrő az állapotstatisztikák kizárásához. Ha nincs megadva, az állapotstatisztikák tartalmazzák az ok, figyelmeztetés és hibaszám az összes alkalmazás gyermek: szolgáltatások, partíciók, replikák, telepített alkalmazások és telepített szolgáltatáscsomagok.

### <a name="api"></a>API
Az alkalmazás állapotának `FabricClient` lehívásához hozzon létre egy és hívja meg a [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) metódust a HealthManager-en.

A következő kód lekéri az alkalmazás állapotát a megadott alkalmazásnévhez (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

A következő kód beszerzi a megadott alkalmazásnév (URI) alkalmazásállapotát, az [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription)értéken megadott szűrőkkel és egyéni házirendekkel.

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
Az alkalmazás állapotának lekért parancsmag a [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával.

A következő parancsmag a **fabric:/WordCount** alkalmazás állapotát adja vissza:

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

A következő PowerShell-parancsmag az egyéni házirendekben halad át. Azt is szűri a gyermekek és események.

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
Az alkalmazás állapotát [get-kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) vagy [postai kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) kaphatja meg, amely a szervezetben leírt állapotházirendeket tartalmaz.

## <a name="get-service-health"></a>Szolgáltatás állapotának beszereznie
Egy szolgáltatásentitás állapotát adja eredményül. Ez tartalmazza a partíció állapotát. Bemenet:

* [Kötelező] A szolgáltatást azonosító szolgáltatásnév (URI).
* [Nem kötelező] Az alkalmazás jegyzékfájl-házirend felülbírálásához használt alkalmazásállapot-házirend.
* [Nem kötelező] Események és partíciók szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményben vissza kell adni őket (például csak hibák, vagy figyelmeztetések és hibák egyaránt). Minden esemény és partíció az entitás összesített állapotának kiértékelésére szolgál, függetlenül a szűrőtől.
* [Nem kötelező] Szűrő az állapotstatisztikák kizárásához. Ha nincs megadva, az állapotstatisztika a szolgáltatás összes partíciójának és replikájának ok, figyelmeztetés és hibaszámát jeleníti meg.

### <a name="api"></a>API
A szolgáltatás állapotának lekérni `FabricClient` az API-n keresztül, hozzon létre egy és hívja meg a [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) metódust a HealthManager.To get healthhealthasync method on its HealthManager.

A következő példa egy megadott szolgáltatásnévvel (URI) rendelkező szolgáltatás állapotát kapja meg:

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

A következő kód beszerzi a szolgáltatás állapotát a megadott szolgáltatásnévhez (URI), szűrőket és egyéni házirendet adva meg a [ServiceHealthQueryDescription-en](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription)keresztül:

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A szolgáltatás állapotának lekért parancsmaga [get-servicefabricservicehealth.](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth) Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával.

A következő parancsmag az alapértelmezett állapotházirendek használatával kapja meg a szolgáltatás állapotát:

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
A szolgáltatás állapotát [get-kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) vagy [postai kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) kaphatja meg, amely a szervezetben leírt állapotházirendeket tartalmaz.

## <a name="get-partition-health"></a>Partíció állapotának beszereznie
Egy partícióentitás állapotát adja vissza. A replika állapotok. Bemenet:

* [Kötelező] A partíciót azonosítót (GUID) azonosító, amely azonosítja a partíciót.
* [Nem kötelező] Az alkalmazás jegyzékfájl-házirend felülbírálásához használt alkalmazásállapot-házirend.
* [Nem kötelező] Események és kópiák szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményben vissza kell adni őket (például csak hibák, vagy figyelmeztetések és hibák egyaránt). Az összes esemény és kópia az entitás összesített állapotának kiértékelésére szolgál, függetlenül a szűrőtől.
* [Nem kötelező] Szűrő az állapotstatisztikák kizárásához. Ha nincs megadva, az állapotstatisztikák azt mutatják, hogy hány replikák rendben van, figyelmeztetés és hiba állapotok.

### <a name="api"></a>API
Az API-n keresztüli partícióállapotának lehívásához hozzon létre egy `FabricClient` és hívja meg a [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) metódust a HealthManager en. Nem kötelező paraméterek megadásához hozza létre a [PartitionHealthQueryDescription metódust.](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription)

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
A partíció állapotának lekért parancsmaga [get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával.

A következő parancsmag a háló összes partíciójának állapotát **kapja:/WordCount/WordCountService** szolgáltatás, és kiszűri a replika állapotát:

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
A partíció állapotát [get-kérelemmel](/rest/api/servicefabric/sfclient-api-getpartitionhealth) vagy [postai kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) kaphatja meg, amely a szervezetben leírt állapotházirendeket tartalmaz.

## <a name="get-replica-health"></a>A replika állapotának beszereznie
Egy állapotalapú szolgáltatásreplika vagy állapotmentes szolgáltatáspéldány állapotát adja vissza. Bemenet:

* [Kötelező] A kópiát azonosítót azonosító partícióazonosító (GUID) és replikaazonosító.
* [Nem kötelező] Az alkalmazás állapotházirend-házirendjének az alkalmazásjegyzék-házirendek felülbírálásához használt paraméterek.
* [Nem kötelező] Az események szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményben vissza kell adni őket (például csak hibák, vagy figyelmeztetések és hibák egyaránt). Az összes esemény az entitás összesített állapotának kiértékelésére szolgál, függetlenül a szűrőtől.

### <a name="api"></a>API
A replika állapotának az `FabricClient` API-n keresztül, hozzon létre egy és hívja meg a [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metódust a HealthManager. Speciális paraméterek megadásához használja a [ReplicaHealthQueryDescription metódust.](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription)

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
A replika állapotának lekért parancsmaga a [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával.

A következő parancsmag a szolgáltatás összes partíciójának elsődleges replika állapotát kapja meg:

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
A replika állapotát [get-kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) vagy [postai kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) kaphatja meg, amely a szervezetben leírt állapotházirendeket tartalmaz.

## <a name="get-deployed-application-health"></a>Telepített alkalmazás állapotának beszereznie
Egy csomópontentitásra telepített alkalmazás állapotát adja vissza. Az üzembe helyezett szolgáltatáscsomag állapotokat tartalmazza. Bemenet:

* [Kötelező] Az alkalmazás neve (URI) és a csomópont neve (karakterlánc), amely azonosítja az üzembe helyezett alkalmazást.
* [Nem kötelező] Az alkalmazás jegyzékfájl-házirendjének felülbírálásához használt alkalmazásállapot-házirend.
* [Nem kötelező] Események és telepített szolgáltatáscsomagok szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményben vissza kell adni őket (például csak hibák, vagy figyelmeztetések és hibák egyaránt). Minden esemény és üzembe helyezett szolgáltatáscsomagok az entitás összesített állapotának kiértékelésére szolgálnak, függetlenül a szűrőtől.
* [Nem kötelező] Szűrő az állapotstatisztikák kizárásához. Ha nincs megadva, az állapotstatisztikák az üzembe helyezett szolgáltatáscsomagok számát mutatják rendben, figyelmeztetésben és hibaállapotban.

### <a name="api"></a>API
Az API-n keresztül egy csomóponton telepített alkalmazás állapotának `FabricClient` lehívásához hozzon létre egy és hívja meg a [GetDeployedApplicationHealthAsync metódust](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) a HealthManageren. Nem kötelező paraméterek megadásához használja [a DeployedApplicationHealthQueryDescription metódust.](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription)

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Az üzembe helyezett alkalmazás állapotának lekért parancsmag a [Get-ServiceFabricDeployedApplicationHealth.](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával. Ha meg szeretné tudni, hogy egy alkalmazás hol van telepítve, futtassa a [Get-ServiceFabricApplicationHealth futtatása,](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) és tekintse meg az üzembe helyezett alkalmazás gyermekek.

A következő parancsmag a **_Node_2-ra**telepített **háló:/WordCount** alkalmazás állapotát kapja.

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
Az üzembe helyezett alkalmazás állapotát [get-kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) vagy [postai kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) kaphatja meg, amely a szervezetben leírt állapotházirendeket tartalmaz.

## <a name="get-deployed-service-package-health"></a>Telepített szolgáltatáscsomag állapotának beszereznie
Egy üzembe helyezett szolgáltatáscsomag-entitás állapotát adja vissza. Bemenet:

* [Kötelező] Az alkalmazás neve (URI), csomópont neve (karakterlánc) és a szolgáltatásjegyzék neve (karakterlánc), amely azonosítja az üzembe helyezett szolgáltatáscsomagot.
* [Nem kötelező] Az alkalmazás jegyzékfájl-házirend felülbírálásához használt alkalmazásállapot-házirend.
* [Nem kötelező] Az események szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményben vissza kell adni őket (például csak hibák, vagy figyelmeztetések és hibák egyaránt). Az összes esemény az entitás összesített állapotának kiértékelésére szolgál, függetlenül a szűrőtől.

### <a name="api"></a>API
Az üzembe helyezett szolgáltatáscsomag állapotának lekérni az `FabricClient` API-n keresztül, hozzon létre egy és hívja meg a [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) metódust a HealthManager en. Nem kötelező paraméterek megadásához használja [a DeployedServicePackageHealthQueryDescription metódust.](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription)

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Az üzembe helyezett szolgáltatáscsomag állapotának lekért parancsmag a [Get-ServiceFabricDeployedServicePackageHealth.](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth) Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával. Az alkalmazások üzembe helyezésének helyének megtekintéséhez futtassa a [Get-ServiceFabricApplicationHealth futtatást,](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) és tekintse meg a telepített alkalmazásokat. Tekintse meg, hogy mely szolgáltatáscsomagok vannak egy alkalmazásban, tekintse meg a [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) kimenetben telepített szolgáltatáscsomag-gyermekeket.

A következő parancsmag a _Node_2-ra telepített **fabric:/WordCount** alkalmazás **WordCountServicePkg** szolgáltatáscsomagjának állapotát **kapja meg.** Az entitás **System.Hosting** jelentésekkel rendelkezik a sikeres szolgáltatáscsomag- és belépésipont-aktiváláshoz, valamint a sikeres szolgáltatástípus-regisztrációhoz.

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
Az üzembe helyezett szolgáltatáscsomag állapota [get-kéréssel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) vagy [postai kéréssel,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) amely a szervezetben leírt állapotházirendeket tartalmaz.

## <a name="health-chunk-queries"></a>Állapotadattömb-lekérdezések
Az állapotadattömb-lekérdezések bemeneti szűrőkönként többszintű fürtgyermekeket adhatnak vissza (rekurzív módon). Támogatja a fejlett szűrőket, amelyek sok rugalmasságot tesznek lehetővé a visszaadandó gyermekek kiválasztásában. A szűrők a gyermekeket az egyedi azonosító vagy más csoportazonosítók és/vagy állapotok alapján adhatják meg. Alapértelmezés szerint nem gyermekek szerepelnek, szemben az egészségügyi parancsokat, amelyek mindig tartalmazzák az első szintű gyermekek.

Az [állapotlekérdezések](service-fabric-view-entities-aggregated-health.md#health-queries) csak a megadott entitás első szintű gyermekeit adják vissza a szükséges szűrők szerint. Ahhoz, hogy a gyermekek gyermekei, meg kell hívnia további egészségügyi API-k minden egyes adottentitás. Hasonlóképpen, adott entitások állapotának lekérni, meg kell hívnia egy-egy állapot API-t minden egyes kívánt entitás. Az adattömblekérdezés speciális szűrése lehetővé teszi, hogy több fontos elemet kérjen egy lekérdezésben, minimalizálva az üzenetek méretét és az üzenetek számát.

Az adattömblekérdezés értéke az, hogy egy hívásban több fürtentitás (potenciálisan a szükséges legfelső szintű root-tól kezdődő összes fürtentitás) állapotának lehívása érhető el. Összetett állapotlekérdezést is kifejezésre juttathat, például:

* Csak a hibás alkalmazásokat adja vissza, és ezekhez az alkalmazásokhoz az összes figyelmeztetési vagy hibai szolgáltatást tartalmazza. A visszaküldött szolgáltatások, tartalmazza az összes partíciót.
* Csak négy alkalmazás állapotát adja vissza, a nevükkel megadva.
* Csak a kívánt alkalmazástípus alkalmazásainak állapotát adja vissza.
* Az összes üzembe helyezett entitás visszaadása egy csomóponton. Visszaadja az összes alkalmazást, a megadott csomóponton telepített összes telepített alkalmazást és az adott csomóponton telepített összes üzembe helyezett szolgáltatáscsomagot.
* Az összes kópia hibás an- Az összes alkalmazást, szolgáltatást, partíciót és csak hibás replikát ad vissza.
* Az összes alkalmazás visszaadása. Egy adott szolgáltatás esetén adja meg az összes partíciót.

Jelenleg az állapottömb lekérdezés csak a fürtentitás hoz elérhető. Egy fürt állapotadattömböt ad vissza, amely a következőket tartalmazza:

* A fürt összesített állapot.
* A bemeneti szűrőket tiszteletben író csomópontok állapotadattömbje.
* A bemeneti szűrőket tiszteletben álló alkalmazások állapotadattömbje. Minden alkalmazás állapotadattömb tartalmaz egy adattömblista az összes szolgáltatást, amely tiszteletben tartja a bemeneti szűrők és egy adattömblista az összes telepített alkalmazások, amelyek tiszteletben tartják a szűrőket. Ugyanez vonatkozik a gyermekek a szolgáltatások és a telepített alkalmazások. Ily módon a fürt összes entitása potenciálisan visszaadható, ha kérik, hierarchikus módon.

### <a name="cluster-health-chunk-query"></a>Fürt állapotadattömblekérdezése
A fürtentitás állapotát adja vissza, és tartalmazza a szükséges gyermekek hierarchikus állapotrészei. Bemenet:

* [Nem kötelező] A csomópontok és a fürtesemények kiértékeléséhez használt fürtállapot-házirend.
* [Nem kötelező] Az alkalmazás állapotházirend-leképezése az alkalmazásjegyzék-házirendek felülbírálásához használt állapotházirendekkel.
* [Nem kötelező] A csomópontok és alkalmazások szűrői, amelyek meghatározzák, hogy mely bejegyzések érdekesek, és az eredményt vissza kell adni. A szűrők egy entitásra/entitáscsoportra vonatkoznak, vagy az adott szinten lévő összes entitásra vonatkoznak. A szűrők listája tartalmazhat egy általános szűrőt és/vagy szűrőket a lekérdezés által visszaadott részletes entitások adott azonosítóihoz. Ha üres, a gyermekek alapértelmezés szerint nem kerülnek visszaadásra.
  További információ a szűrőkről a [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) és [az ApplicationHealthStateFilter oldalon.](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter) Az alkalmazásszűrők rekurzív módon megadhatják a speciális szűrőket a gyermekek számára.

Az adattömb eredménye tartalmazza a szűrőket tiszteletben lévő gyermekeket.

Jelenleg az adattömb lekérdezés nem ad vissza nem megfelelő állapotú kiértékelések vagy entitásesemények. Ez a további információ a meglévő fürt állapotlekérdezés használatával szerezhető be.

### <a name="api"></a>API
Fürt állapotadattömbjének `FabricClient` lehívásához hozzon létre egy és hívja meg a [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metódust a **HealthManager en.** A [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) az állapotházirendek és a speciális szűrők leírására szolgál.

A következő kód leáll a fürt állapotadattömbspeciális szűrőkkel.

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
A fürt állapotának lekért parancsmag a [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag használatával.

A következő kód csak akkor kap csomópontokat, ha azok a hiba, kivéve egy adott csomópont, amely mindig vissza kell adni.

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

A következő parancsmag leveszi a fürttömbalkalmazás-szűrőkkel.

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

A következő parancsmag visszaadja az összes üzembe helyezett entitást egy csomóponton.

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
A fürt állapotadat-egy [GET-kéréssel](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) vagy egy [POST-kérelemmel](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) kaphat, amely a szervezetben ismertetett állapotházirendeket és speciális szűrőket tartalmaz.

## <a name="general-queries"></a>Általános lekérdezések
Az általános lekérdezések adott típusú Service Fabric-entitások listáját adják vissza. Az API-n keresztül (a **FabricClient.QueryManager**metódusain keresztül), a PowerShell-parancsmagok és a REST segítségével vannak elérhetővé tenni. Ezek a lekérdezések több összetevő allekérdezéseit összesítik. Ezek közül az egyik a [health store,](service-fabric-health-introduction.md#health-store)amely feltölti az összesített állapot minden lekérdezés eredménye.  

> [!NOTE]
> Az általános lekérdezések az entitás összesített állapotát adják vissza, és nem tartalmaznak gazdag állapotadatokat. Ha egy entitás nem kifogástalan állapotú, az állapotlekérdezések nyomon követésével az összes egészségügyi információkat, beleértve az eseményeket, a gyermek állapota és a nem megfelelő állapotok.
>
>

Ha az általános lekérdezések egy entitás ismeretlen állapotának visszaadása, lehetséges, hogy a health store nem rendelkezik teljes adatokkal az entitásról. Az is lehetséges, hogy a health Store-ra irányuló segédlekérdezés nem volt sikeres (például kommunikációs hiba történt, vagy az állapottároló takarta). Kövesse nyomon az entitás állapotlekérdezését. Ha a segédlekérdezés átmeneti hibákat, például hálózati problémákat észlelt, ez a nyomon követési lekérdezés sikeres lehet. Azt is adhat további részleteket a health Store arról, hogy miért az entitás nincs kitéve.

Az entitások **HealthState-et** tartalmazó lekérdezései a következők:

* Csomópontlista: A fürt (lapozott) listacsomópontjait adja eredményül.
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Alkalmazáslista: A fürtben lévő alkalmazások listáját adja eredményül (lapozva).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Szolgáltatáslista: Egy alkalmazás (lapozott) szolgáltatásainak listáját adja eredményül.
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partíciólista: Egy szolgáltatás partícióinak listáját adja vissza (lapozott).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Replikalista: Egy partícióreplikák listáját adja vissza (lapozott).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Telepített alkalmazáslista: A csomóponton telepített alkalmazások listáját adja vissza.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Telepített szolgáltatáscsomagok listája: Egy telepített alkalmazásban lévő szolgáltatáscsomagok listáját adja vissza.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Egyes lekérdezések lapozható eredményeket adnak vissza. Ezeknek a lekérdezéseknek a visszaadása a [PagedList\<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Ha az eredmények nem férnek el egy üzenet, csak egy oldal jelenik meg, és egy ContinuationToken, amely nyomon követi, ahol enumerálás leállt. Továbbra is hívja meg ugyanazt a lekérdezést, és adja át a folytatási jogkivonatot az előző lekérdezésből a következő eredmények lekéréséhez.

### <a name="examples"></a>Példák
A következő kód leáll a fürt sérült alkalmazásai:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

A következő parancsmag lekéri az alkalmazás részleteit a fabric:/WordCount alkalmazás. Figyelje meg, hogy az állapot figyelmeztetés.

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

A következő parancsmag a szolgáltatásokat hibaállapottal kapja le:

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

## <a name="cluster-and-application-upgrades"></a>Fürt- és alkalmazásfrissítések
A fürt és az alkalmazás figyelt frissítése során a Service Fabric ellenőrzi az állapotot, hogy minden kifogástalan állapotban maradjon. Ha egy entitás nem megfelelő állapotú a konfigurált állapotházirendek használatával kiértékelve, a frissítés frissítés-specifikus házirendeket alkalmaz a következő művelet meghatározásához. A frissítés szüneteltethető a felhasználói beavatkozás (például a hibafeltételek javítása vagy a házirendek módosítása) érdekében, vagy automatikusan visszaállhat az előző jó verzióra.

A *fürt* frissítése során lekaphatja a fürt frissítési állapotát. A frissítési állapot nem megfelelő állapotú kiértékeléseket tartalmaz, amelyek rámutatnak arra, hogy mi nem megfelelő a fürtben. Ha a frissítés állapotproblémák miatt visszaáll, a frissítési állapot megjegyzi az utolsó nem megfelelő állapotú okokat. Ez az információ segíthet a rendszergazdáknak annak kivizsgálásában, hogy mi volt a hiba a frissítés visszagörgetése vagy leállítása után.

Hasonlóképpen az *alkalmazás* frissítése során a nem megfelelő állapotú kiértékelések az alkalmazás frissítési állapotát tartalmazza.

Az alábbi ábrán látható egy módosított háló:/WordCount alkalmazás alkalmazásfrissítési állapota. Egy figyelő hibát jelentett az egyik replikáján. A frissítés visszaáll, mert az állapotellenőrzéseket nem tartják be.

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

További információ a [Service Fabric alkalmazásfrissítésről.](service-fabric-application-upgrade.md)

## <a name="use-health-evaluations-to-troubleshoot"></a>Hibaelhárítás az állapotértékelésekhez
Ha probléma merül fel a fürtvagy egy alkalmazás, tekintse meg a fürt vagy az alkalmazás állapotát, hogy pontosan mi a baj. A nem megfelelő állapotú értékelések részletesen ismertetik, hogy mi váltotta ki az aktuális nem megfelelő állapot. Ha szükséges, leáshat a nem megfelelő gyermekentitások a kiváltó ok azonosításához.

Fontolja meg például egy alkalmazás nem kifogástalan állapotú, mert van egy hibajelentés az egyik replikák. A következő Powershell-parancsmag a nem megfelelő állapotú értékeléseket mutatja:

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

A replika további információkért tekintse meg a replikát:

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
> A nem megfelelő állapotú értékelések azt mutatják, az első oka az entitás kiértékelése a jelenlegi állapot. Előfordulhat, hogy több más esemény is elindítja ezt az állapotot, de ezek nem jelennek meg az értékelésekben. További információkért részletezze az állapotentitások, hogy kitaláljuk, a fürt összes nem megfelelő állapotú jelentéseket.
>
>

## <a name="next-steps"></a>További lépések
[Rendszerállapot-jelentések használata a hibaelhárítás során](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Egyéni Service Fabric-állapotjelentések hozzáadása](service-fabric-report-health.md)

[A szolgáltatás állapotának jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric alkalmazásfrissítése](service-fabric-application-upgrade.md)
