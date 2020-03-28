---
title: Service Fabric-fürt figyelése az Azure-ban
description: Ebben az oktatóanyagban megtudhatja, hogyan figyelheti a fürt szolgáltatásfabric-események megtekintésével, az EventStore API-k lekérdezésével, a perf-számlálók figyelésével és az állapotjelentések megtekintésével.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: ab58d622511e0d5793eb6df312bc3fd6dd15bfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75376630"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Service Fabric-fürt figyelése az Azure-ban

A figyelés és a diagnosztika kritikus fontosságú a számítási feladatok fejlesztése, tesztelése és üzembe helyezése szempontjából bármilyen felhőalapú környezetben. Ez az oktatóanyag egy sorozat második része, és bemutatja, hogyan figyelheti és diagnosztizálhatja a Service Fabric-fürt események, teljesítményszámlálók és állapotjelentések használatával.   További információt a [fürtfigyelésről](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) és az [infrastruktúra figyeléséről](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring)szóló áttekintésben talál.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Service Fabric eseményeinek megtekintése
> * A fürtesemények EventStore API-inak lekérdezése
> * Infrastruktúra/perf számlálók gyűjtése
> * Fürtállapot-jelentések megtekintése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban sablon használatával
> * Fürt figyelése
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Telepítse [az Azure Powershellt](https://docs.microsoft.com/powershell/azure/install-Az-ps) vagy [az Azure CLI-t.](/cli/azure/install-azure-cli)
* Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása 
* A fürt [diagnosztikai gyűjteményének](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) beállítása
* Az [EventStore szolgáltatás](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) engedélyezése a fürtben
* Az [Azure Monitor naplóinak és a fürt Log Analytics-ügynökének](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) konfigurálása

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Service Fabric-események megtekintése az Azure Monitor naplóinak használatával

Az Azure Monitor naplói összegyűjtik és elemzik a felhőben üzemeltetett alkalmazások és szolgáltatások telemetriai adatait, és elemzési eszközöket biztosít a rendelkezésre állásuk és teljesítményük maximalizálásához. Lekérdezések futtatásához az Azure Monitor naplók betekintést nyerhet, és hibaelhárítás, mi történik a fürtben.

A Service Fabric Analytics-megoldás eléréséhez nyissa meg az [Azure Portalon,](https://portal.azure.com) és válassza ki azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics-megoldást.

Válassza ki a **ServiceFabric(mysfomsworkspace) erőforrást.**

**Az áttekintésben** az egyes engedélyezett megoldásokhoz, köztük a Service Fabrichez is, grafikon formájában jelennek meg a csempék. Kattintson a **Service Fabric** graph a Service Fabric Analytics-megoldás folytatásához.

![Service Fabric-megoldás](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Az alábbi képen a Service Fabric Analytics-megoldás kezdőlapja látható. Ez a kezdőlap pillanatkép-nézetet nyújt afürtben zajló eseményekről.

![Service Fabric-megoldás](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Ha engedélyezte a diagnosztikát a fürt létrehozásakor, láthatja a 

* [Service Fabric-fürtesemények](service-fabric-diagnostics-event-generation-operational.md)
* [Megbízható szereplők programozási modell események](service-fabric-reliable-actors-diagnostics.md)
* [Megbízható szolgáltatások programozási modell eseményei](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A Service Fabric-események mellett részletesebb rendszeresemények is gyűjthetők [a diagnosztikai bővítmény konfigurációjának frissítésével.](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>A Szolgáltatásháló-események megtekintése, beleértve a csomópontokon végrehajtott műveleteket is

A Service Fabric Analytics lapon kattintson a **fürtesemények**grafikonjára.  Az összegyűjtött rendszeresemények naplói megjelennek. Referenciaként ezek a **WADServiceFabricSystemEventsTable** az Azure Storage-fiókban, és hasonlóképpen a megbízható szolgáltatások és szereplők események lásd a következő a megfelelő táblákból.
    
![Lekérdezés operatív csatornája](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

A lekérdezés a Kusto lekérdezési nyelvet használja, amelyet módosíthat a keresett finomításérdekében. Például a fürt csomópontjain végrehajtott összes művelet megkereséséhez használhatja a következő lekérdezést. Az alább használt eseményazonosítók az [operatív csatorna eseményeinek hivatkozásában](service-fabric-diagnostics-event-generation-operational.md)találhatók.

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A Kusto lekérdezési nyelv erőllág. Íme néhány további hasznos lekérdezés.

*ServiceFabricEvent-lekérdezési* tábla létrehozása felhasználó által definiált függvényként a lekérdezést a ServiceFabricEvent alias függvényként való mentésével:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Az elmúlt órában rögzített működési események visszaadása:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Működési események visszaadása az EventId == 18604 és az EventName == 'NodeDownOperational' azonosítóval:
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Működési események visszaadása az EventId == 18604 és az EventName == 'NodeUpOperational' azonosítóval:
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Állapotjelentéseket ad vissza healthstate == 3 (hiba) értékkel, és további tulajdonságokat nyer ki az EventMessage mezőből:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Események időtáblázatának visszaadása az EventId != 17523 értékkel:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

A Service Fabric működési eseményeinek beszereznie az adott szolgáltatással és csomónnal összesítve:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

A Service Fabric-események számának lehetővé tétele EventId / EventName szerint egy erőforrásközi lekérdezés használatával:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>A Service Fabric alkalmazáseseményeinek megtekintése

Megtekintheti a megbízható szolgáltatások és a fürtön telepített megbízható szereplők alkalmazások eseményeit.  A Service Fabric Analytics lapon kattintson az **alkalmazásesemények**grafikonjára.

Futtassa a következő lekérdezést a megbízható szolgáltatási alkalmazások eseményeinek megtekintéséhez:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Megtekintheti a különböző eseményeket, amikor a szolgáltatás runasync elindul, és befejeződött, amely általában történik a központi telepítések és frissítések.

![Service Fabric megoldás megbízható szolgáltatások](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

A megbízható szolgáltatás eseményeit is megtalálhatja a ServiceName == "fabric:/Watchdog/WatchdogService" segítségével:

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Megbízható szereplő események megtekinthetők hasonló módon:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Részletesebb események megbízható szereplőkhöz való konfigurálásához `scheduledTransferKeywordFilter` módosíthatja a konfigurációban a fürtsablon diagnosztikai bővítményéhez. Ezek relevanciaértékeia [megbízható szereplőkre vonatkozó referenciában található](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Teljesítményszámlálók megtekintése az Azure Monitor-naplókkal
Teljesítményszámlálók megtekintéséhez nyissa meg az [Azure Portalon,](https://portal.azure.com) és az erőforráscsoport, amelyben létrehozta a Service Fabric Analytics-megoldás. 

Válassza ki a **ServiceFabric(mysfomsworkspace)** erőforrást, majd **a Log Analytics-munkaterületet,** majd **a Speciális beállításokat.**

Kattintson **az Adatok**menü Windows **teljesítményszámlálói parancsára.** Van egy lista az alapértelmezett számlálók közül választhat, hogy engedélyezze, és beállíthatja a gyűjtemény időközéist is. [További teljesítményszámlálókat](service-fabric-diagnostics-event-generation-perf.md) is hozzáadhat a gyűjtéshez. A cikk a megfelelő [article](/windows/desktop/PerfCtrs/specifying-a-counter-path)formátumra hivatkozik. Kattintson **a Mentés**gombra, majd **az OK gombra.**

Zárja be a Speciális beállítások panelt, és az **Általános** fejléc alatt válassza a **Munkaterület összegzése** lehetőséget. Minden egyes engedélyezett megoldások egy grafikus csempe, beleértve a Service Fabric egy. Kattintson a **Service Fabric** graph a Service Fabric Analytics-megoldás folytatásához.

Vannak grafikus csempék a működési csatorna és a megbízható szolgáltatási események. A kiválasztott számlálók hoz beáramló adatok grafikus ábrázolása a **Csomópont-mérőszámok területen**jelenik meg. 

Válassza ki a **tároló metrika** grafikon további részletek megtekintéséhez. A teljesítményszámláló-adatokat a fürteseményekhez hasonlóan is lekérdezheti, és szűrheti a csomópontokat, a perf számláló nevét és az értékeket a Kusto lekérdezési nyelv használatával.

## <a name="query-the-eventstore-service"></a>Az EventStore szolgáltatás lekérdezése
Az [EventStore szolgáltatás](service-fabric-diagnostics-eventstore.md) lehetővé teszi a fürt vagy a számítási feladatok állapotának megértését egy adott időpontban. Az EventStore egy állapotalapú Service Fabric szolgáltatás, amely a fürtből származó eseményeket tart karban. Az események a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST és API-k között vannak elérhetők. Az EventStore közvetlenül lekérdezi a fürtöt, hogy diagnosztikai adatokat kapjon a fürt bármely entitásáról az EventStore-ban elérhető események teljes listájának megtekintéséhez olvassa el a [Service Fabric-események című](service-fabric-diagnostics-event-generation-operational.md)témakört.

Az EventStore API-k programozott módon lekérdezhetők a [Service Fabric ügyfélkódtár](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)használatával.

Íme egy példa kérelem az összes fürtesemény2018-04-03T18:00:00Z és 2018-04-04T18:00:00Z között, a GetClusterEventListAsync függvényen keresztül.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Íme egy másik példa, amely lekérdezi a fürt állapotát és az összes csomóponti eseményt 2018 szeptemberében, és kinyomtatja őket.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Fürt állapotának figyelése
A Service Fabric egy [állapotmodellt](service-fabric-health-introduction.md) vezet be olyan állapotentitásokkal, amelyeken a rendszer-összetevők és a figyelők jelenthetik a figyelésáltal figyelt helyi feltételeket. Az [állapottároló](service-fabric-health-introduction.md#health-store) összesíti az összes állapotadatot annak meghatározásához, hogy az entitások kifogástalanállapotúak-e.

A fürt automatikusan feltöltődik a rendszerösszetevők által küldött állapotjelentésekkel. További [információ: A rendszerállapot-jelentések használata a hibaelhárításhoz.](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

A Service Fabric minden egyes támogatott [entitástípushoz](service-fabric-health-introduction.md#health-entities-and-hierarchy)elérhetővé teszi az állapotlekérdezéseket. Az API-n keresztül érhetők el a [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), a PowerShell-parancsmagok és a REST metódusai használatával. Ezek a lekérdezések teljes körű állapotinformációt adnak vissza az entitásról: az összesített állapot, az entitás állapotának állapota, a gyermek állapotállapota (ha van), a nem megfelelő állapotú értékelések (ha az entitás nem kifogástalan), és a gyermekek állapotstatisztikái (amikor alkalmazandó).

### <a name="get-cluster-health"></a>Fürtállapotának beszereznie
A [Get-ServiceFabricClusterHealth parancsmag](/powershell/module/servicefabric/get-servicefabricclusterhealth) a fürtentitás állapotát adja vissza, és tartalmazza az alkalmazások és csomópontok (a fürt gyermekei) állapotát.  Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster parancsmag](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)használatával.

A fürt állapota 11 csomópont, a rendszeralkalmazás és a fabric:/Szavazás a leírtak szerint konfigurálva.

A következő példa kap fürt állapotát alapértelmezett állapotházirendek használatával. A 11 csomópont kifogástalan, de a fürt összesített állapot a hiba, mert a fabric:/Voting alkalmazás hiba. Vegye figyelembe, hogy a nem megfelelő állapotú értékelések részletesen ismertetik az összesített állapotot kiváltó feltételeket.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

A következő példa a fürt állapotát egy egyéni alkalmazásházirend használatával kapja meg. Szűri az eredményeket, hogy csak alkalmazásokat és csomópontokat kapjon hiba vagy figyelmeztetés esetén. Ebben a példában nem ad vissza csomópontokat, mivel ezek mindegyike kifogástalan. Csak a fabric:/Voting alkalmazás tartja tiszteletben az alkalmazásszűrőt. Mivel az egyéni házirend határozza meg, hogy a figyelmeztetéseket a háló:/Szavazási alkalmazás hibáinak tekintsék, az alkalmazás kiértékelése hibás, és így a fürt is.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>A csomópont állapotának beszereznie
A [Get-ServiceFabricNodeHealth parancsmag](/powershell/module/servicefabric/get-servicefabricnodehealth) egy csomópontentitás állapotát adja vissza, és tartalmazza a csomóponton jelentett állapoteseményeket. Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster parancsmag](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)használatával. A következő példa egy adott csomópont állapotát kapja meg az alapértelmezett állapotházirendek használatával:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

A következő példa a fürt összes csomócsomójának állapotát kapja meg:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>A rendszerszolgáltatás állapotának beszereznie 

A rendszerszolgáltatások összesített állapotának beszerezni:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Service Fabric eseményeinek megtekintése
> * A fürtesemények EventStore API-inak lekérdezése
> * Infrastruktúra/perf számlálók gyűjtése
> * Fürtállapot-jelentések megtekintése

Ezután a következő oktatóanyagba lépve megtudhatja, hogyan méretezhet egy fürtöt.
> [!div class="nextstepaction"]
> [Fürt skálázása](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
