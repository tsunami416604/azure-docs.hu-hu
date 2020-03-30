---
title: Fürtesemények lekérdezése az EventStore API-k használatával
description: Megtudhatja, hogy miként használhatja az Azure Service Fabric EventStore API-kat platformesemények lekérdezésére
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 48350caef6bdaafda9aff7ac776d67b314aeaf8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614400"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>A fürtesemények EventStore API-inak lekérdezése

Ez a cikk bemutatja, hogyan lehet lekérdezni a Service Fabric 6.2-es és újabb verzióban elérhető EventStore API-kat – ha többet szeretne megtudni az EventStore szolgáltatásról, olvassa el az [EventStore szolgáltatás áttekintése című témakört.](service-fabric-diagnostics-eventstore.md) Jelenleg az EventStore szolgáltatás csak az elmúlt 7 nap adataihoz férhet hozzá (ez a fürt diagnosztikai adatmegőrzési házirendjén alapul).

>[!NOTE]
>Az EventStore API-k ga, mint a Service Fabric 6.4-es verziója csak az Azure-ban futó Windows-fürtök.

Az EventStore API-k közvetlenül érhetők el egy REST-végponton keresztül, vagy programozott módon. A lekérdezéstől függően számos paraméter szükséges a megfelelő adatok összegyűjtéséhez. Ezek a paraméterek általában a következők:
* `api-version`: a használt EventStore API-k verziója
* `StartTimeUtc`: meghatározza annak az időszaknak a kezdetét, amelyet érdekel,
* `EndTimeUtc`: az időszak vége

Ezeken a paramétereken kívül választható paraméterek is rendelkezésre állnak, mint például:
* `timeout`: felülbírálja az alapértelmezett 60 másodperces időtúltöltést a kérelemművelet végrehajtásához
* `eventstypesfilter`: ez lehetővé teszi, hogy szűrjen bizonyos eseménytípusokra
* `ExcludeAnalysisEvents`: ne adja vissza az "Elemzés" eseményeket. Alapértelmezés szerint az EventStore-lekérdezések lehetőség szerint "elemzési" eseményekkel térnek vissza. Elemzési események gazdagabb operatív csatorna események, amelyek további környezetben vagy információkat túl egy rendszeres Service Fabric-esemény, és több mélységet.
* `SkipCorrelationLookup`: ne keressen potenciális korrelált eseményeket a fürtben. Alapértelmezés szerint az EventStore megpróbálja korrelálni az eseményeket egy fürtön, és az eseményeket, ha lehetséges, összekapcsolja. 

A fürtminden egyes entitása események lekérdezései lehetnek. A típus összes entitásának eseményeit is lekérdezheti. Lekérdezheti például egy adott csomópont vagy a fürt összes csomópontjának eseményeit. Az entitások aktuális készlete, amelyhez eseményeket kérdezhet le (a lekérdezés felépítésének módjával):
* Fürt:`/EventsStore/Cluster/Events`
* Csomópontok:`/EventsStore/Nodes/Events`
* Csomópont:`/EventsStore/Nodes/<NodeName>/$/Events`
* Alkalmazások:`/EventsStore/Applications/Events`
* Alkalmazás:`/EventsStore/Applications/<AppName>/$/Events`
* Szolgáltatások:`/EventsStore/Services/Events`
* Szolgáltatás:`/EventsStore/Services/<ServiceName>/$/Events`
* Partíciók:`/EventsStore/Partitions/Events`
* Partíció:`/EventsStore/Partitions/<PartitionID>/$/Events`
* Replikák:`/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replika:`/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Amikor egy alkalmazás vagy szolgáltatás nevére hivatkozik, a lekérdezésnek nem kell tartalmaznia a "fabric:/" Előtag. Továbbá, ha az alkalmazás vagy a szolgáltatás nevei egy "/" bennük, váltson át a "~", hogy a lekérdezés működik. Ha például az alkalmazás "fabric:/App1/FrontendApp" néven jelenik meg, `/EventsStore/Applications/App1~FrontendApp/$/Events`az alkalmazásspecifikus lekérdezések a következőképpen épülnek fel.
>Emellett a szolgáltatások állapotjelentései ma megjelennek a megfelelő `DeployedServiceHealthReportCreated` alkalmazás alatt, így a megfelelő alkalmazásentitás eseményeit szeretné lekérdezni. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Az EventStore lekérdezése REST API-végpontokon keresztül

Az EventStore közvetlenül egy REST-végponton `GET` keresztül is `<your cluster address>/EventsStore/<entity>/Events/`lekérdezhető a következő kérésekkel: .

A között és `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`a között lévő összes fürtesemény lekérdezéséhez például a következőképpen fog kinézni:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Ez vagy nem ad vissza eseményeket, vagy a jsonban visszaadott események listáját:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Itt láthatjuk, hogy `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`a kettő között és a , ez a `"CurrentClusterVersion": "0.0.0.0:"` fürt `"TargetClusterVersion": "6.2:1.0"`sikeresen befejezte az első frissítést, amikor először felállt, a , a . `"OverallUpgradeElapsedTimeInMs": "120196.5212"`

## <a name="query-the-eventstore-programmatically"></a>Az EventStore programozott lekérdezése

Az EventStore programozott módon is lekérdezhető a [Service Fabric ügyfélkódtáron](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)keresztül.

Miután beállította a Service Fabric-ügyfelet, lekérdezheti az eseményeket az EventStore-hoz az így:`sfhttpClient.EventStore.<request>`

Íme egy példa kérelem az `2018-04-03T18:00:00Z` összes `2018-04-04T18:00:00Z`fürtesemények `GetClusterEventListAsync` között, és a függvényen keresztül.

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

## <a name="sample-scenarios-and-queries"></a>Mintaforgatókönyvek és lekérdezések

Íme néhány példa arra, hogyan hívhatja meg az Event Store REST API-kat a fürt állapotának megértéséhez.

*Fürtfrissítések:*

Ha meg szeretné tekinteni, hogy a fürt legutóbb sikeresen vagy a múlt héten megkísérelt frissítésre történt-e, lekérdezheti a fürt nemrég befejezett frissítéseihez szükséges API-kat az EventStore "ClusterUpgradeCompleted" eseményeinek lekérdezésével:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Fürtfrissítési problémák:*

Hasonlóképpen, ha problémák merültek fel a legutóbbi fürtfrissítéssel kapcsolatban, lekérdezheti a fürtentitás összes eseményét. Látni fogja a különböző események, beleértve a frissítések kezdeményezése, és minden UD, amelyhez a frissítés gördült keresztül sikeresen. A visszaállítás i. és a megfelelő állapotesemények eseményeiis megjelennek. A következő lekérdezést szeretné használni ehhez:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*A csomópont állapota megváltozik:*

Ha látni szeretné a csomópont állapotának változásait az elmúlt néhány napban – amikor a csomópontok fel- vagy lefelé mentek, vagy aktiválták vagy inaktiválták őket (akár a platform, akár a chaos szolgáltatás, akár a felhasználói bevitel által) – használja a következő lekérdezést:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alkalmazásesemények:*

A legutóbbi alkalmazástelepítések és -frissítések nyomon követhető. A fürt összes alkalmazáseseményének megtekintéséhez használja az alábbi lekérdezést:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Egy alkalmazás történeti állapota:*

Amellett, hogy csak az alkalmazás életciklus-eseményeit látja, előfordulhat, hogy egy adott alkalmazás állapotára vonatkozó előzményadatokat is szeretne látni. Ezt úgy teheti meg, hogy megadja azt az alkalmazásnevet, amelynek adatait össze kívánja gyűjteni. Ezzel a lekérdezéssel lekérdezi az `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`összes alkalmazásállapot-eseményt: . Ha azt szeretné, hogy az állapotesemények, amelyek lejárt (elment telt az `,ApplicationHealthReportExpired` életidő (TTL)), add hozzá a végén a lekérdezés, szűrni két típusú események.

*Történelmi egészség a "myApp" összes szolgáltatása számára:*

Jelenleg a szolgáltatások állapotjelentési `DeployedServicePackageNewHealthReport` eseményei a megfelelő alkalmazásentitás szerinti eseményekként jelennek meg. A szolgáltatások "App1" szolgáltatásának működéséhez használja a következő lekérdezést:`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Partíció újrakonfigurálása:*

A fürtben történt partíciómozgások megtekintéséhez kérdezze meg `PartitionReconfigured` az eseményt. Ez segíthet annak kifejtésében, hogy mely számítási feladatok futottak, amelyeken adott időpontokban a csomópont, a fürt ben lévő problémák diagnosztizálásakor. Íme egy mintalekérdezés, amely ezt teszi:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Káosz szolgáltatás:*

Van egy esemény, amikor a Chaos szolgáltatás elindul, vagy leáll, amely elérhető a fürt szintjén. A Chaos szolgáltatás legutóbbi használatának megtekintéséhez használja a következő lekérdezést:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

