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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117055"
---
A [Tartós funkciók konfigurációs](../articles/azure-functions/durable-functions-overview.md)beállításai .

### <a name="durable-functions-1x"></a>Tartós funkciók 1.x

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

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Tartós funkciók 2.x

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

A feladatközpont nevének betűvel kell kezdődnie, és csak betűkből és számokból állhat. Ha nincs megadva, a függvényalkalmazás alapértelmezett feladatközpont-neve **DurableFunctionsHub.** További információt a Feladatközpontok című [témakörben](../articles/azure-functions/durable-functions-task-hubs.md)talál.

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternatív [feladatközpont-nevek](../articles/azure-functions/durable-functions-task-hubs.md) segítségével elkülönítheti a több tartós függvény alkalmazások egymástól, akkor is, ha ugyanazt a tároló háttérrendszer használ.|
|controlQueueBatchSize|32|A vezérlővárólistából egyszerre lehúzandó üzenetek száma.|
|controlQueueBufferThreshold|256|A memóriában egyszerre pufferelhető vezérlővárósorok üzeneteinek száma, amikor is a diszpécser várni fog a további üzenetek sorba állításával.|
|partitionCount |4|A vezérlővárólista partíciószáma. Lehet pozitív egész szám 1 és 16 között.|
|controlQueueVisibilityTimeout |5 perc|A várólistán lévő vezérlővárósorok üzeneteinek láthatósági időoutja.|
|workItemQueueVisibilityTimeout |5 perc|A várólistán lévő munkaelemek várólistán lévő üzeneteinek láthatósági időmeghosszabbítása.|
|maxConcurrentActivityFunctions |10X a processzorok száma az aktuális gépen|Az egy gazdapéldányon egyidejűleg feldolgozható tevékenységfüggvények maximális száma.|
|maxConcurrentOrchestratorFunctions |10X a processzorok száma az aktuális gépen|Az egyetlen gazdapéldányon egyidejűleg feldolgozható orchestrator függvények maximális száma.|
|maxQueuePollingInterval|30 másodperc|A maximális vezérlő- és munkaelem-várólista lekérdezési időköze *hh:mm:ss* formátumban. A magasabb értékek magasabb üzenetfeldolgozási késéseket eredményezhetnek. Az alacsonyabb értékek magasabb tárolási költségeket eredményezhetnek a megnövekedett tárolási tranzakciók miatt.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Az azure storage-beli erőforrások kezeléséhez használt Azure Storage-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve.|
|trackingStoreConnectionStringName||Az Előzmények és példányok táblákhoz használandó kapcsolati karakterlánc neve. Ha nincs megadva, a rendszer a `azureStorageConnectionStringName` kapcsolatot használja.|
|trackingStoreNamePrefix||Az Előzmények és példányok táblákhoz `trackingStoreConnectionStringName` megadott előtag. Ha nincs beállítva, az alapértelmezett `DurableTask`előtag értéke a . Ha `trackingStoreConnectionStringName` nincs megadva, akkor az Előzmények és `hubName` példányok táblák az értéket használják `trackingStoreNamePrefix` előtagként, és a rendszer figyelmen kívül hagyja a beállításokat.|
|traceInputsAndOutputs |hamis|Egy érték, amely azt jelzi, hogy nyomon kell-e követni a függvényhívások bemeneteit és kimeneteit. A függvényvégrehajtási események nyomon követése során az alapértelmezett viselkedés a függvényhívások szerializált bemeneteinek és kimeneteinek bájtjainak felvétele. Ez a viselkedés minimális információt nyújt arról, hogy a bemenetek és kimenetek hogyan néznek ki anélkül, hogy felduzzadnának a naplókban, vagy véletlenül felfedne bizalmas információkat. Ha ezt a tulajdonságot true értékre állítja, az alapértelmezett függvénynaplózás naplózza a függvénybemenetek és -kimenetek teljes tartalmát.|
|logReplayEsemények|hamis|Egy érték, amely azt jelzi, hogy kell-e írni vezénylési visszajátszási események Application Insights.|
|eventGridTopicEndpoint ||Az Azure Event Grid egyéni témakör-végpont URL-címe. Ha ez a tulajdonság be van állítva, vezénylési életciklus-értesítési események közzé téve ezt a végpontot. Ez a tulajdonság támogatja az alkalmazásbeállítások felbontását.|
|eventGridKeySettingName ||Az Azure Event Grid egyéni témakörével való hitelesítéshez használt kulcsot tartalmazó `EventGridTopicEndpoint`alkalmazásbeállítás neve a itt.|
|eventGridPublishRetryCount|0|Nem sikerül újrapróbálkoznia, ha az Eseményrács témakörében való közzététel sikertelen.|
|eventGridPublishRetryInterval|5 perc|Az Event Grid *a hh:mm:ss* formátumban teszi közzé az újrapróbálkozási időközt.|
|eventGridPublishEventTypes||Az Eseményrácsban közzétehető eseménytípusok listája. Ha nincs megadva, az összes eseménytípus közzétételre kerül. Az engedélyezett `Started` `Completed`értékek `Failed` `Terminated`közé tartozik a , , , .|
|useGracefulShutdown|hamis|(Előzetes verzió) Engedélyezze a szabályos leállítást, így csökken az állomásleállások folyamatközbeni függvények végrehajtása sikertelensítésének esélyét.|

Ezek közül a beállítások közül sok a teljesítmény optimalizálására van. További információt a [Teljesítmény és méretezés](../articles/azure-functions/durable-functions-perf-and-scale.md)című témakörben talál.
