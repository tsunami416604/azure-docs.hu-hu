---
title: A fürthöz kapcsolódó események EventStore API-val az Azure Service Fabric-fürtök lekérdezés |} Microsoft Docs
description: Az Azure Service Fabric EventStore API-lekérdezés használata platform események
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: af97385981c61f32c4136921d3cf14a526fc6ddb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="query-eventstore-apis-for-cluster-events"></a>A fürthöz kapcsolódó események lekérdezés EventStore API-k

Ez a cikk bemutatja, hogyan adhat lekérdezni a EventStore API-k érhetők el a Service Fabric 6.2 verziót, és később - Ha szeretne többet megtudni a EventStore szolgáltatás tekintse meg a [EventStore szolgáltatás áttekintése](service-fabric-diagnostics-eventstore.md). Jelenleg a EventStore szolgáltatás csak hozzáférhetnek a adatok az elmúlt 7 napban (alapul a fürt diagnosztika adatmegőrzési házirend).

>[!NOTE]
>Től Service Fabric 6.2 verziót. a EventStore API-k jelenleg csak Azure-on futó Windows-fürtök előzetes verzióját. Ez a funkció a Linux, valamint az önálló fürtök eljárás dolgozunk.

Közvetlenül a REST-végpont szolgáltatáson keresztül, illetve programozott módon, a EventStore API-k segítségével érhető el. Attól függően, hogy a lekérdezés nincsenek számos paraméter, a megfelelő adatok gyűjtéséhez szükséges. Ezek általában a következők:
* `api-version`: a verzió a EventStore API-k használata
* `StartTimeUtc`: határozza meg az érdekes megnézzük áll időszak kezdete
* `EndTimeUtc`: az időszak vége

Ezek mellett érhetők el opcionális paraméterek, többek között:
* `timeout`: a kérelem művelet végrehajtása időtúllépés alapértelmezett 60 másodperc felülbírálása
* `eventstypesfilter`: Ez lehetővé teszi adott eseménytípusok szűrhet lehetőséget
* `ExcludeAnalysisEvents`: "Analysis" esemény nem adják vissza. Alapértelmezés szerint EventStore lekérdezések "analysis" események ad vissza, ha lehetséges – elemzési eseményeket gazdagabb működési csatorna az eseményeket, amelyek további környezet vagy információkat rendszeres Service Fabric-esemény túl tartalmazhat, és adja meg a mélyebben is.
* `SkipCorrelationLookup`: nem keresse meg a fürt lehetséges kapcsolódó eseményeket. Alapértelmezés szerint a EventStore megpróbál események összefüggéseket fürtön belül, és kapcsolja össze az események, amikor lehetséges. 

Minden entitás fürtben lehet események lekérdezéseket. Minden entitás típusú események is lekérdezhet. Például alapján is kereshet egy adott csomópont, vagy az összes csomópont események a fürtön. Az aktuális készletében lévő entitások, amelynek alapján is kereshet események van (hogyan a lekérdezés volna strukturált):
* Fürt: `/EventsStore/Cluster/Events`
* Csomópontok: `/EventsStore/Nodes/Events`
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
>Való hivatkozáskor az egy alkalmazás vagy szolgáltatás neve, a lekérdezés nem kell adnia a "fabric: /" előtag. Továbbá, ha az alkalmazás vagy szolgáltatás nevét a "/" bennük, váltson úgy, hogy a "~" tartani a lekérdezés működik. Ha az alkalmazás mutatja például "fabric: / App1/FrontendApp", az alkalmazás adott lekérdezések volna strukturált, mint a `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Emellett állapotjelentések szolgáltatások ma jelenik meg alatt az megfelelő alkalmazás, így Ön szeretné lekérdezni az `DeployedServiceHealthReportCreated` eseményeket a megfelelő alkalmazás entitás. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>A REST API-végpontokon keresztül EventStore lekérdezése

Közvetlenül a REST-végpont, keresztül EventStore azáltal, hogy lekérdezhesse `GET` kérelem: `<your cluster address>/EventsStore/<entity>/Events/`.

Például ahhoz, hogy minden fürthöz kapcsolódó események között kereshet `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`, a kérelem alábbihoz hasonlóan fog kinézni:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.2-preview&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Hiba vagy ez sikerült vissza, ha az események nem érhetők el, vagy ha a lekérdezés sikeres volt, a JSON-ban visszaadott események láthatja:

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

Itt láthatja, amelyek között `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`, a fürt sikeresen befejeződött az első frissítése, azt először lett formája, amikor a `"CurrentClusterVersion": "0.0.0.0:"` való `"TargetClusterVersion": "6.2:1.0"`, a `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>A EventStore programozott módon lekérdezése

Is lekérheti a EventStore keresztül programozott módon, a [Service Fabric ügyféloldali kódtár](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Miután a Service Fabric-ügyfélnek, állítsa be, kérdezheti események ehhez hasonló EventStore elérésével: ` sfhttpClient.EventStore.<request>`

Íme egy példa egy kérelem, az összes fürt közötti események `2018-04-03T18:00:00Z` és `2018-04-04T18:00:00Z`, keresztül a `GetClusterEventListAsync` függvény.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

## <a name="sample-scenarios-and-queries"></a>Mintaforgatókönyvek és lekérdezések

Íme néhány példa a hogyan hívása az esemény Store REST API-k tudni, hogy a fürt állapota.

1. Fürt frissítése:

A fürt sikeres volt, vagy kísérlet történt múlt héten frissítendő legutóbbi megtekintéséhez az API-k nemrég befejeződött frissítésre lekérdezheti a fürthöz, az "ClusterUpgradeComplete" események a EventStore lekérdezésével: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeComplete`

1. Fürt frissítési problémák:

Hasonlóképpen ha problémák merültek fel egy újabb Fürtfrissítés, sikerült kérdezze le a fürt entitás összes eseményt. Látni fogja, különféle eseményeket, beleértve a frissítéseket és minden UD, amelyre a frissítés állítva keresztül sikeresen. A pont, ahol az események a visszaállítási lépések, a megfelelő állapotával kapcsolatos események is megtekintheti. Ez a lekérdezés ezen használhatja: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Csomópont állapota változásai:

A csomópont állapota az elmúlt keresztül néhány nap - amikor csomópontok probléma merült fel vagy le, vagy volt aktív vagy inaktív (a platform, a chaos szolgáltatás, vagy a felhasználói bevitel) – használja a következő lekérdezést: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Alkalmazás események:

A legutóbbi alkalmazástelepítéseket és a frissítések is követheti. Minden alkalmazás megtekintéséhez használja a következő lekérdezést a kapcsolódó események a fürt: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Az alkalmazás korábbi állapotát:

Csak jelent a alkalmazás életciklusa események, mellett is érdemes lehet egy adott alkalmazás állapotát a korábbi adatok megtekintéséhez. Ehhez adja meg az alkalmazás nevét, amelyekhez az adatok gyűjtéséhez. Használja ezt a lekérdezést az alkalmazás állapotának események: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.2-preview&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ProcessApplicationReport`. Ha szeretne állapotával kapcsolatos események, előfordulhat, hogy lejárt (letelt az idő átadott élettartam (TTL)), adja hozzá `,ExpiredDeployedApplicationEvent` a lekérdezést, a két típusú események szűrése végéig.

1. Az összes szolgáltatás "myApp" korábbi állapotát:

Jelenleg jelentés események állapotfigyelő szolgáltatások megjelennek, `DeployedServiceHealthReportCreated` eseményeket a megfelelő alkalmazás entitás alatt. Hogyan a szolgáltatások "App1" rendelkezik lett teljesítenek megtekintéséhez használja a következő lekérdezést: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServiceHealthReportCreated`

1. A partíció újrakonfigurálása:

Láthatja az összes partíció mozgását, és ismételje meg a fürt lekérdezése a `ReconfigurationCompleted` esemény. Ennek segítségével mérje fel, milyen munkaterhelések futtatott melyik csomópont meghatározott időközönként, ha ad diagnosztizálása ki, a fürtön. Íme egy minta lekérdezést, amelyet, amely: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.2-preview&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigurationCompleted`

1. Chaos szolgáltatás:

Létre eseményt, ha a szolgáltatás indítása vagy leállítása, amely a Chaos elérhetővé a fürt szintjén van. Olvassa el a legutóbbi a Chaos szolgáltatást, használja a következő lekérdezést: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

