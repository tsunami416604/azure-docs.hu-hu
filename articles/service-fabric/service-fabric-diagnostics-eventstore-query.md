---
title: A fürthöz kapcsolódó események EventStore API-val az Azure Service Fabric-fürtök lekérdezési |} A Microsoft Docs
description: Ismerje meg, hogyan használja az Azure Service Fabric EventStore API-k lekérdezéshez tartozó platform események
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: facbcd6def7451ca83bdf00fe9b7c7cac2c74945
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879947"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>EventStore API-k lekérdezni a fürthöz kapcsolódó események

Ez a cikk bemutatja, hogyan érhetők el a Service Fabric verziója 6.2 és újabb – Ha az EventStore szolgáltatással kapcsolatos további információért lásd: EventStore API-k a [EventStore szolgáltatás áttekintése](service-fabric-diagnostics-eventstore.md). Jelenleg az EventStore szolgáltatás csak hozzáférhetnek a adatok az elmúlt 7 napban (alapul, a fürt diagnosztika az adatmegőrzési házirend).

>[!NOTE]
>Az EventStore API-k 6.4 verzió csak az Azure-on futó Windows fürtök esetén a Service Fabric-től általánosan elérhető.

Az EventStore API-k érhetők el közvetlenül a REST-végponton keresztül, vagy programozott módon. A lekérdezéstől függően vannak, amelyek szükségesek a megfelelő adatokat gyűjt több paramétert. Ezek a paraméterek általában a következők:
* `api-version`: a verzióját az EventStore API-k használata
* `StartTimeUtc`: határozza meg az Önt érdeklő megnézzük az időszak kezdete
* `EndTimeUtc`: az időszak vége

Ezeket a paramétereket, valamint opcionális paraméterek érhető el, például:
* `timeout`: felülbírálják az alapértelmezett 60 második időtúllépési értéket a kért művelet végrehajtásához
* `eventstypesfilter`: Ez lehetővé teszi az adott eseménytípusok szűrése
* `ExcludeAnalysisEvents`: ' "eseményeket nem adott vissza. Alapértelmezés szerint az EventStore lekérdezések adja vissza "elemzése" eseményekkel rendelkező ahol csak lehetséges. Elemzés események olyan gazdagabb műveleti csatorna események, amelyek további környezet vagy egy normál Service Fabric-esemény túli információkat tartalmaznak, és adja meg részletesebben olvashat róluk.
* `SkipCorrelationLookup`: ne keresse a lehetséges kapcsolódó eseményeket, a fürtben. Alapértelmezés szerint az EventStore megkísérli az események összevetését a fürt, és kapcsolja össze az eseményeket, amikor csak lehetséges. 

Minden entitás egy fürtben lehetnek események lekérdezések. A típus összes entitáshoz eseményeket is lekérdezheti. Például lekérdezheti, ha egy adott csomópont, vagy az összes csomópont események a fürtben. A jelenlegi olyan entitások, amelynek események lekérdezhető, (az a lekérdezés hogyan kellene strukturálni):
* Fürt: `/EventsStore/Cluster/Events`
* Csomópont: `/EventsStore/Nodes/Events`
* Csomópont: `/EventsStore/Nodes/<NodeName>/$/Events`
* Alkalmazások: `/EventsStore/Applications/Events`
* Alkalmazás: `/EventsStore/Applications/<AppName>/$/Events`
* Szolgáltatások: `/EventsStore/Services/Events`
* Szolgáltatás: `/EventsStore/Services/<ServiceName>/$/Events`
* Partíciók: `/EventsStore/Partitions/Events`
* Partíció: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Replikák: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* A replika: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Amikor egy alkalmazás vagy szolgáltatás neve hivatkozik, a lekérdezés nem kell tartalmaznia a "fabric: /" előtaggal. Emellett, ha az alkalmazás vagy szolgáltatás neve a "/" őket, váltson, hogy a "~" a lekérdezés működő tartani. Ha az alkalmazás megjelenik-e például "fabric: / App1/FrontendApp", az alkalmazás adott lekérdezések lenne strukturált, mint `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Ezenkívül rendszerállapot-jelentések szolgáltatások ma jelennek meg meg az alkalmazást, így Ön szeretné lekérdezni a `DeployedServiceHealthReportCreated` eseményeket a megfelelő alkalmazás entitás. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>A REST API-végpontokon keresztül EventStore lekérdezése

Az EventStore közvetlenül egy REST-végpont használatával lekérdezheti azáltal, hogy `GET` kérelmeket: `<your cluster address>/EventsStore/<entity>/Events/`.

Például közé eső összes fürt lekérdezéséhez `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`, a kérelem néz ki:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Ez vagy vissza nem esemény vagy a visszaadott JSON-események listája:

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

Itt látható, amelyek között `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`, a fürt sikeresen befejeződött a felhasználónak először frissíteniük, akkor volt először parancsból, amikor a `"CurrentClusterVersion": "0.0.0.0:"` való `"TargetClusterVersion": "6.2:1.0"`, a `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Az EventStore programozott módon lekérdezése

Is lekérdezheti az EventStore, automatizáltan a [Service Fabric ügyféloldali kódtár](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

A Service Fabric-ügyfél beállítása után események lekérdezheti az ilyen EventStore elérésével:
`sfhttpClient.EventStore.<request>`

Íme egy kérelem (példa), a fürt összes események között `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`, keresztül a `GetClusterEventListAsync` függvény.

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

## <a name="sample-scenarios-and-queries"></a>Mintaforgatókönyvek és lekérdezések

Íme néhány példa a hogyan hívhatja az esemény Store REST API-kat a fürt állapotának megismeréséhez.

*Fürt frissítése:*

Az utolsó időpont, a fürt sikeres volt, vagy frissítve az elmúlt hét próbált megtekintéséhez a az EventStore az "ClusterUpgradeCompleted" események lekérdezésével lekérdezheti az API-k a nemrégiben befejezett frissítések a fürthöz:
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Fürt frissítési problémák:*

Hasonlóképpen ha legutóbbi a fürtfrissítések problémái vannak, kérdezheti a fürt entitáshoz tartozó összes esemény. Látni fogja a különféle eseményeket, beleértve a frissítéseket, és minden egyes UD, amelyhez a frissítés állítva keresztül sikeresen. Látni fogja emellett események a pont, amelyen a visszaállítás elindítása és a megfelelő hálózatállapot-események. Ez a lekérdezés ezt használja:
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Csomópont állapota változásai:*

Tekintse meg a csomópont állapota az elmúlt néhány nap - amikor csomópontok hiba történt a felfelé és lefelé, vagy volt aktív vagy inaktív (a platform, a chaos szolgáltatást, vagy a felhasználói bevitel) – használja a következő lekérdezést:
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Alkalmazásesemények:*

A legutóbbi alkalmazástelepítéseket és a frissítések is követheti. A fürt összes alkalmazása események megtekintéséhez használja a következő lekérdezést:
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Az alkalmazás korábbi állapotát:*

Csak az alkalmazás életciklus-események lát, mellett is érdemes korábbi adatok alapján egy adott alkalmazás állapotának megtekintéséhez. Ez az alkalmazás nevét, amelynek meg szeretné az adatok gyűjtéséhez megadásával teheti meg. Ez a lekérdezés használatával az alkalmazás minden állapotesemény: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Ha szeretne érvényessége hálózatállapot-események tartalmazzák (megszűnt idejüket átadott élettartam (TTL)), adja hozzá `,ApplicationHealthReportExpired` , két típusú események szűrése a lekérdezés végére.

*Korábbi állapot "myApp" szolgáltatásokhoz:*

Jelenleg jelentés állapotesemények szolgáltatások megjelenjen `DeployedServicePackageNewHealthReport` eseményeket a megfelelő alkalmazás-entitás alapján. Megtudhatja, hogyan a szolgáltatások "Az App1" rendelkezik lett mivel, használja a következő lekérdezést:
`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*A partíció újrakonfigurálása:*

Az összes a partíció áthelyezések száma –, és ismételje meg a fürt lekérdezni a `PartitionReconfigured` esemény. Ez segítségére lehet döntse el, milyen számítási feladatokat futtatott melyik csomópont megadott időpontban, amikor a fürt diagnosztizálása problémák. Itt látható, amely, amely mintalekérdezés:
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*A Chaos szolgáltatás:*

Nincs létre eseményt, amikor a káosz, a szolgáltatás elindult vagy leállt, hogy elérhető-e a fürt szintjén. A Chaos szolgáltatás közelmúltbeli használatát megtekintéséhez használja a következő lekérdezést:
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

