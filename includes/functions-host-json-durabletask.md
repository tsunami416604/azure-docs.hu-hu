---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80117055"
---
[Durable functions](../articles/azure-functions/durable-functions-overview.md)konfigurációs beállításai.

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2. x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

A feladat-hub nevének betűvel kell kezdődnie, és csak betűkből és számokból állhat. Ha nincs megadva, a functions-alkalmazás alapértelmezett neve **DurableFunctionsHub**. További információ: [Task hubok](../articles/azure-functions/durable-functions-task-hubs.md).

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Az alternatív [feladatok központi](../articles/azure-functions/durable-functions-task-hubs.md) neve felhasználható több Durable functions-alkalmazás elkülönítésére, még akkor is, ha ugyanazt a tárolási hátteret használják.|
|controlQueueBatchSize|32|A vezérlési sorból egyszerre lekérdezett üzenetek száma.|
|controlQueueBufferThreshold|256|A memóriában egyszerre pufferelt ellenőrzési üzenetsor-üzenetek száma, amelynél a kézbesítő a további üzenetek elszállítása előtt várakozik.|
|partitionCount |4|A vezérlő várólistájának partícióinak száma. Egy 1 és 16 közötti pozitív egész szám lehet.|
|controlQueueVisibilityTimeout |5 perc|A várólista-vezérlési üzenetsor üzeneteinek láthatósági időtúllépése.|
|workItemQueueVisibilityTimeout |5 perc|A várólistán lévő munkaelem üzenetsor-üzeneteinek láthatósági időtúllépése.|
|maxConcurrentActivityFunctions |a processzorok száma az aktuális gépen (10X)|Az egyazon gazdagép-példányon egyidejűleg feldolgozható tevékenység-függvények maximális száma.|
|maxConcurrentOrchestratorFunctions |a processzorok száma az aktuális gépen (10X)|Azon Orchestrator-függvények maximális száma, amelyek egyszerre dolgozhatók fel egyetlen gazdagép-példányon.|
|maxQueuePollingInterval|30 másodperc|A maximális vezérlési és a munkaelem-várólista lekérdezési időköze a *hh: PP: mm* formátumban. A magasabb értékek az üzenetek feldolgozási késleltetését eredményezik. Az alacsonyabb értékek nagyobb tárolási költségekhez vezethetnek a megnövekedett tárolási tranzakciók miatt.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Annak az alkalmazás-beállításnak a neve, amely a mögöttes Azure Storage-erőforrások kezeléséhez használt Azure Storage-beli kapcsolódási karakterláncot tartalmaz.|
|trackingStoreConnectionStringName||Az előzmények és a példányok tábláihoz használandó kapcsolódási karakterlánc neve. Ha nincs megadva, a `azureStorageConnectionStringName` rendszer a kapcsolatokat használja.|
|trackingStoreNamePrefix||Az előzmények és a példányok tábláihoz használandó előtag, `trackingStoreConnectionStringName` ha meg van adva. Ha nincs beállítva, az alapértelmezett előtag érték lesz `DurableTask`. Ha `trackingStoreConnectionStringName` nincs megadva, az előzmények és a példányok táblázata az `hubName` értéket előtagként fogja használni, és a rendszer figyelmen `trackingStoreNamePrefix` kívül hagyja a beállítást.|
|traceInputsAndOutputs |hamis|Egy érték, amely azt jelzi, hogy nyomon kell-e követni a függvények bemeneteit és kimeneteit. Az alapértelmezett viselkedés, ha a nyomkövetési függvény végrehajtásának eseményei között szerepel a bájtok száma a szerializált bemenetekben és a függvények hívásainak kimenetében. Ez a viselkedés minimális információt nyújt arról, hogy a bemenetek és a kimenetek hogyan néznek ki a naplók közelítése vagy a bizalmas adatok szándékos kitéve nélkül. Ha ezt a tulajdonságot True értékre állítja, az alapértelmezett függvény naplózza a függvény bemeneteit és kimeneteit teljes tartalmának naplózására.|
|logReplayEvents|hamis|Egy érték, amely azt jelzi, hogy a rendszer megírja-e a hangelőkészítési eseményeket Application Insightsba.|
|eventGridTopicEndpoint ||Egy Azure Event Grid egyéni témakörbeli végpont URL-címe Ha ez a tulajdonság be van állítva, a rendszer a munkafolyamatok életciklus-értesítési eseményeit közzéteszi a végponton. Ez a tulajdonság támogatja az Alkalmazásbeállítások feloldását.|
|eventGridKeySettingName ||Annak az alkalmazás-beállításnak a neve, amely a Azure Event Grid egyéni témakörrel való hitelesítéshez használt `EventGridTopicEndpoint`kulcsot tartalmazza.|
|eventGridPublishRetryCount|0|Az újrapróbálkozások száma, ha a Event Grid témakörben való közzététel meghiúsul.|
|eventGridPublishRetryInterval|5 perc|A Event Grid az újrapróbálkozási időközt a *óó: PP: SS* formátumban teszi közzé.|
|eventGridPublishEventTypes||Az Event Grid közzétenni kívánt eseménytípus listája. Ha nincs megadva, az összes eseménytípus közzé lesz téve. Az engedélyezett értékek `Started`a `Completed`következők `Failed`: `Terminated`,,,.|
|useGracefulShutdown|hamis|Előnézet A zökkenőmentes leállítás lehetővé teszi, hogy a gazdagépek leállításának esélyét a folyamaton kívüli függvények végrehajtása okozza.|

Ezeknek a beállításoknak a nagy része a teljesítmény optimalizálása. További információ: [teljesítmény és skálázás](../articles/azure-functions/durable-functions-perf-and-scale.md).
