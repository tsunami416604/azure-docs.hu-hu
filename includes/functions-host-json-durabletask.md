---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58137639"
---
A konfigurációs beállítások [Durable Functions](../articles/azure-functions/durable-functions-overview.md).

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
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

A feladat értesítésiközpont-nevek kell betűvel kezdődhet, és csak betűkből és számokból állhat. Ha nincs megadva, a függvényalkalmazás az alapértelmezett feladat hub neve: **DurableFunctionsHub**. További információkért lásd: [hubs feladat](../articles/azure-functions/durable-functions-task-hubs.md).

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternatív [feladat hub](../articles/azure-functions/durable-functions-task-hubs.md) nevek segítségével elkülönítheti egymástól, több Durable Functions-alkalmazás akkor is, ha az általuk használt tároló ugyanazzal a háttérrendszerrel.|
|controlQueueBatchSize|32|A vezérlő várólista lekéréshez egyszerre üzenetek száma.|
|partitionCount |4|A partíciók száma az ellenőrzési várólista. Egy 1 és 16 közötti pozitív egész lehet.|
|controlQueueVisibilityTimeout |5 perc|A várólistából kivéve vezérlés üzenetsorbeli üzenetek láthatósági időkorlátot.|
|workItemQueueVisibilityTimeout |5 perc|A munkahelyi el távolítva a sorból elem üzenetsorbeli üzenetek láthatósági időkorlátot.|
|maxConcurrentActivityFunctions |10 x az aktuális gépen processzorok száma|Tevékenységfüggvényeket egyetlen gazdagép-példány egy időben feldolgozható maximális számát.|
|maxConcurrentOrchestratorFunctions |10 x az aktuális gépen processzorok száma|Az orchestrator-függvények, amelyek egyetlen gazdagép-példány egy időben feldolgozható maximális számát.|
|maxQueuePollingInterval|30 másodperc|Az irányítást és a munkaelem várólista lekérdezési időközét az *óó* formátumban. A magasabb értékek magasabb üzenetfeldolgozási késéseket okozhat. Alacsonyabb érték nagyobb tárolási tranzakciók miatt magasabb tárolási költségek eredményezhet.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Az alapul szolgáló Azure Storage-erőforrások kezelésére szolgáló Azure Storage kapcsolati karakterláncát alkalmazásbeállítás neve.|
|trackingStoreConnectionStringName||Az előzmények és példányok táblák használata a kapcsolati karakterlánc neve. Ha nincs megadva, a `azureStorageConnectionStringName` kapcsolat használatát.|
|trackingStoreNamePrefix||Az előtag, az előzmények és példányok használandó táblák mikor `trackingStoreConnectionStringName` van megadva. Ha nincs megadva, az alapértelmezett előtag érték lesz `DurableTask`. Ha `trackingStoreConnectionStringName` nincs megadva, majd az előzmények és példányok táblákban fogja használni a `hubName` előtag, valamint minden olyan beállítás értéke `trackingStoreNamePrefix` figyelmen kívül hagyja.|
|traceInputsAndOutputs |false|E lépések bemeneteit és kimeneteit a függvényhívások nyomkövetési jelző érték beolvasása. Az alapértelmezett viselkedést, ha a nyomkövetés a függvény végrehajtási eseményeket, hogy a szerializált bemeneteit és kimeneteit a függvényhívások felvétel bájtok száma. Ez a viselkedés a bemenetek és kimenetek kinézni a naplók puffadás vagy véletlenül közzéteheti a bizalmas adatok nélkül minimális információkat biztosít. Ez a tulajdonság true értékre való állítására az alapértelmezett függvény naplózási jelentkezhetnek függvény bemeneti és kimeneti teljes tartalmát.|
|logReplayEvents|false|Vezénylési visszajátszását beírni az Application Insights-e jelző érték beolvasása.|
|eventGridTopicEndpoint ||A végpont URL-címét egy Azure Event Griddel egyéni témakört. Ha ez a tulajdonság értéke, orchestration életciklus-értesítési esemény közzé lesz téve, ennek a végpontnak. Ez a tulajdonság támogatja az alkalmazásbeállítások megoldás.|
|eventGridKeySettingName ||Az Azure Event Griddel egyéni témakörre, amely a hitelesítéshez használt kulcs tartalmazó alkalmazásbeállítás neve `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Sikertelen lesz, ha az Event Grid-témakör közzétételét újrapróbálkozások száma.|
|eventGridPublishRetryInterval|5 perc|Az Event Grid közzéteszi az újrapróbálkozási időköznek a *óó* formátumban.|
|eventGridPublishEventTypes||Az Event Gridbe közzététele eseménytípusok listáját. Ha nincs megadva, minden eseménytípushoz közzé lesz téve. Megengedett értékek: `Started`, `Completed`, `Failed`, `Terminated`.|

Ezek a beállítások számos teljesítmény optimalizálásához. További információkért lásd: [teljesítmény és méretezhetőség](../articles/azure-functions/durable-functions-perf-and-scale.md).