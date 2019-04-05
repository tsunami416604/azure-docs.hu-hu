---
title: Az Azure Service Fabric-fürt monitorozása |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan figyelheti a fürt Service Fabric-események megtekintése az EventStore API-k lekérdezése, teljesítményszámlálók figyelése és rendszerállapot-jelentések megtekintése.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: 9838c6e31e3bb7031d98e615fd96049f22dd8d30
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045645"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Az Azure Service Fabric-fürt monitorozása

A monitoring and diagnostics létfontosságúak fejlesztéséhez, teszteléséhez és bármilyen felhőalapú környezetben a számítási feladatok üzembe helyezése. Ez az oktatóanyag egy sorozat második része, és bemutatja, hogyan figyelheti és diagnosztizálhatja a Service Fabric-fürt, események, a teljesítményszámlálókat és a rendszerállapot-jelentések használatával.   További információkért olvassa el az Áttekintés [fürtfigyelést](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) és [infrastruktúra-figyelési](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Service Fabric-események megtekintése
> * EventStore API-k lekérdezni a fürthöz kapcsolódó események
> * Infrastruktúra/gyűjtése teljesítményszámlálók figyelése
> * Fürt állapotjelentések megtekintése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) az Azure-ban sablon használatával
> * -Fürt monitorozása
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítés [az Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) vagy [az Azure CLI](/cli/azure/install-azure-cli).
* Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* A telepítő [diagnosztikai gyűjteményét](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) a fürt
* Engedélyezze a [EventStore szolgáltatás](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) a fürtben
* Konfigurálása [Azure Monitor-naplók és a Log Analytics-ügynököket](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) a fürt

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Az Azure Monitor-naplók segítségével Service Fabric-események megtekintése

Az Azure Monitor naplóira gyűjti és elemzi az alkalmazások és szolgáltatások a felhőben üzemeltetett származó telemetriai adatok, és segítséget nyújtanak a rendelkezésre állás és teljesítmény maximalizálása elemzésére szolgáló eszközöket biztosít. Az Azure Monitor-naplók elemzése és hibaelhárítása, mi történik a fürtön lévő lekérdezéseket is futtathat.

A Service Fabric-elemzés megoldás eléréséhez nyissa meg a [az Azure portal](https://portal.azure.com) , és válassza ki az erőforráscsoportot, amelyben létrehozta a Service Fabric-elemzés megoldás.

Válassza ki az erőforrást **ServiceFabric(mysfomsworkspace)**.

A **áttekintése** csempék formájában grafikon, lát minden, a megoldások engedélyezve van, amelyek közül az egyik a Service fabric. Kattintson a **Service Fabric** graph, hogy a Service Fabric-elemzés megoldás.

![A Service Fabric-megoldás](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Az alábbi képen látható a Service Fabric-elemzés megoldás kezdőlapján. A kezdőlap nyújt a pillanatkép, hogy mi történik a fürtön.

![A Service Fabric-megoldás](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Ha engedélyezte a diagnosztikai fürt létrehozásakor, láthatja az események 

* [A Service Fabric-fürthöz kapcsolódó események](service-fabric-diagnostics-event-generation-operational.md)
* [A Reliable Actors programozási modell események](service-fabric-reliable-actors-diagnostics.md)
* [A Reliable Services programozási modell események](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A beépített Service Fabric-események, valamint részletesebb rendszeresemények által gyűjtött [a diagnosztikai bővítmény, a konfiguráció frissítésével](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Nézet Service Fabric-események, beleértve a csomópontokon műveletek

A Service Fabric-elemzés oldalon kattintson a diagramon az **Fürtesemények**.  A naplókat a rendszer minden olyan események, összegyűjtött alkalmazásproblémát jelennek meg. Referenciaként az alábbiak a **WADServiceFabricSystemEventsTable** az Azure Storage-fiókot, és ehhez hasonlóan a megbízható szolgáltatások és az actors esemény ezután megjelenik megfelelő táblák származnak.
    
![Lekérdezés műveleti csatorna](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

A lekérdezés a Kusto lekérdezési nyelvet, amely módosíthatja, amit keresett szűkítése céljából használja. Ha például a fürtben található csomópontok végzett összes művelet megkereséséhez használhatja a következő lekérdezést. Az alábbiakban használt eseményazonosítók találhatók a [műveleti csatorna események referencia](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A Kusto-lekérdezési nyelve, hatékony. Az alábbiakban néhány egyéb hasznos lekérdezést.

Hozzon létre egy *ServiceFabricEvent* , felhasználó által definiált függvény menti a lekérdezés ServiceFabricEvent aliasú függvényében keresési táblázat:

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

Az elmúlt órában rögzített visszatérési működési események:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Vissza az eseményazonosító működési eseményeit 18604 és EventName == "NodeDownOperational" ==:
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Vissza az eseményazonosító működési eseményeit 18604 és EventName == "NodeUpOperational" ==:
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Visszaadja a rendszerállapot-jelentések a HealthState == 3 (hiba), és a további tulajdonságok kinyerése a EventMessage mező:

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

Vissza a diagram az események az eseményazonosító! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Kérje le a Service Fabric műveleti események összesíteni az adott szolgáltatás és a csomópont:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Száma eseményazonosító szerint a Service Fabric-események, render / erőforrások közötti lekérdezéssel EventName:

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

### <a name="view-service-fabric-application-events"></a>A Service Fabric-alkalmazás eseményeinek megtekintése

A reliable services és reliable actors eseményei is megtekintheti a fürtön üzembe helyezett alkalmazások.  A Service Fabric-elemzés oldalon kattintson a diagram a **alkalmazásesemények**.

A reliable services-alkalmazások eseményei megtekintéséhez a következő lekérdezés futtatásával:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

A különböző események láthatja, ha a szolgáltatás runasync elkezdődött és befejeződött, amely általában akkor fordul elő a telepítésekkel és frissítésekkel.

![A Service Fabric megoldást a Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Eseményeket is megtalálja a reliable Services-ServiceName == "fabric: / figyelő/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Reliable actors-események hasonló módon is megtekinthetők:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
A reliable actors részletesebb események konfigurálásához módosítsa a `scheduledTransferKeywordFilter` a diagnosztikai bővítmény a fürt sablonban a Config. Ezek a következők az értékeket a részletek a [a reliable actors-események referencia](service-fabric-reliable-actors-diagnostics.md#keywords).

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

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Teljesítményszámlálók megtekintése az Azure Monitor naplóira
Teljesítményszámlálók megtekintéséhez nyissa meg a [az Azure portal](https://portal.azure.com) és az erőforráscsoportot, amelyben létrehozta a Service Fabric-elemzés megoldás. 

Válassza ki az erőforrást **ServiceFabric(mysfomsworkspace)**, majd **Log Analytics-munkaterület**, majd **speciális beállítások**.

Kattintson a **adatok**, majd kattintson a **Windows-teljesítményszámlálók**. A beállítással engedélyezi alapértelmezett számlálókat tartalmazó listát, és beállíthatja a gyűjtési időköze túl. Azt is megteheti [további teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md) gyűjtéséhez. A kiszolgálócím formátuma hivatkozik ez [cikk](/windows/desktop/PerfCtrs/specifying-a-counter-path). Kattintson a **mentése**, majd kattintson a **OK**.

Zárja be a Speciális beállítások panelt, és válassza ki **munkaterület összefoglalás** alatt a **általános** fejléc. Minden, a megoldás nincs engedélyezve van egy grafikus csempét, beleértve a Service fabric egy. Kattintson a **Service Fabric** graph, hogy a Service Fabric-elemzés megoldás.

Nincsenek olyan grafikus csempék műveleti csatorna és a reliable services-események. A kijelölt számlálók alatt jelenik meg a beérkező adatok grafikus ábrázolását **Csomópontmetrikák**. 

Válassza ki a **tároló metrika** graph további részletek megtekintéséhez. A hasonlóan a fürthöz kapcsolódó események és a csomópontok, teljesítményoptimalizált számláló neve és a Kusto-lekérdezési nyelv segítségével értékek alapján végezhet szűrést a teljesítményszámláló-adatokat is lekérdezhet.

## <a name="query-the-eventstore-service"></a>Az EventStore szolgáltatás lekérdezése
A [EventStore szolgáltatás](service-fabric-diagnostics-eventstore.md) lehetővé teszi a fürt vagy az adott számítási feladatok állapotának megjelenítése az idő. Az EventStore egy állapotalapú Service Fabric-szolgáltatás, amely fenntartja az események a fürtből. Az események keresztül érhetők el a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST és API-k. Az EventStore lekérdezi a fürthöz, közvetlenül elérhető az EventStore események teljes listáját megtekintheti a fürt minden entitás tekintse meg diagnosztikai adatok lekérése [Service Fabric-események](service-fabric-diagnostics-event-generation-operational.md).

Az EventStore API-k használatával programozott módon kérhetők le a [Service Fabric ügyféloldali kódtár](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Itt van egy kérelem (példa) események között 2018 fürt összes-04-03T18:00:00Z és a 2018-04-04T18:00:00Z GetClusterEventListAsync függvényével.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Íme egy másik példa, amely lekérdezi a 2018 szeptember a fürt állapotának és az összes csomópont esemény, és kiírja őket.

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


## <a name="monitor-cluster-health"></a>Fürtállapot monitorozása
Bemutatja a Service Fabric egy [állapotmodell](service-fabric-health-introduction.md) egészségügyi entitásokkal, mely rendszer összetevőit és watchdogs is jelentés helyi feltételek, amelyek figyelése. A [health Store adatbázisban](service-fabric-health-introduction.md#health-store) összesíti az összes egészségügyi adatokat annak meghatározására, hogy e entitások kifogástalan állapotú.

A fürt rendszer automatikusan kitölti a rendszer összetevők által küldött rendszerállapot-jelentések. További információk: [rendszerállapot-jelentések használata a hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

A Service Fabric állapotlekérdezések mutatja az egyes támogatott [entitástípusok](service-fabric-health-introduction.md#health-entities-and-hierarchy). A módszerekkel, az API-n keresztül hozzáférhetők [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell-parancsmagok és a többi. Ezeket a lekérdezéseket az entitás teljes állapota információt ad vissza: az összesített állapotát, entitás hálózatállapot-események, gyermek állapotokat (ha alkalmazható), nem megfelelő értékelések (ha az entitás állapota nem kifogástalan) és gyermekek egészségügyi statisztika (Ha érvényes).

### <a name="get-cluster-health"></a>Fürtállapot beolvasása
A [Get-ServiceFabricClusterHealth parancsmag](/powershell/module/servicefabric/get-servicefabricclusterhealth) a fürt entitás állapotát adja vissza, és tartalmazza az alkalmazások és csomópontok (a fürt children) állapotúak.  Először csatlakozzon a fürthöz a [Connect-ServiceFabricCluster parancsmag](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

A fürt állapota 11 csomópontokat, a rendszer, alkalmazás és a fabric: / Voting leírtak szerint konfigurálva.

Az alábbi példa lekéri a fürt állapotának alapértelmezett házirendek használatával. A 11 csomópontok kifogástalan állapotú, de az összesített fürt állapotának hiba azért, mert a fabric: / Szavazóalkalmazás hiba van. Vegye figyelembe, hogy a nem megfelelő értékelések hogyan részletekkel szolgálnak a feltételeket, amelyeknek aktivált összesített állapotát.

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

Az alábbi példa lekéri a fürt állapotának-egyéni alkalmazás-házirendek használatával. Csak az alkalmazások és csomópontok hiba vagy figyelmeztetés eredményeket szűri. Ebben a példában nincsenek csomópontok ad vissza, mivel ezek az összes kifogástalan állapotú. Csak a fabric: / Szavazóalkalmazás tiszteletben tartja az alkalmazások szűrő. Mivel az egyéni házirend megadja, hogy fontolja meg a háló hibák, figyelmeztetések: / Szavazóalkalmazás, az alkalmazás ki lesz értékelve, mint a hiba, és így az a fürt.

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

### <a name="get-node-health"></a>Csomópont állapotának lekérése
A [Get-ServiceFabricNodeHealth parancsmag](/powershell/module/servicefabric/get-servicefabricnodehealth) egy csomópont entitás állapotát adja vissza, és tartalmazza a csomóponton jelentett hálózatállapot-események. Először csatlakozzon a fürthöz használatával a [Connect-ServiceFabricCluster parancsmag](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). Az alábbi példa lekéri egy adott csomópont állapotát az alapértelmezett házirendek:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

Az alábbi példa lekéri az összes csomópont állapotát a fürt:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Rendszer szolgáltatásállapot beolvasása 

Kérje le a helyrendszeri szolgáltatások összesített állapotát:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Service Fabric-események megtekintése
> * EventStore API-k lekérdezni a fürthöz kapcsolódó események
> * Infrastruktúra/gyűjtése teljesítményszámlálók figyelése
> * Fürt állapotjelentések megtekintése

Ezután folytassa a következő oktatóanyag egy fürt méretezése.
> [!div class="nextstepaction"]
> [Fürt skálázása](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
