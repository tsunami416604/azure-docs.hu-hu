---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 92eb13165326f44432f09322ea97f3cee5ccec2b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251243"
---
A konfigurációs beállítások [Durable Functions](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

A feladat értesítésiközpont-nevek kell betűvel kezdődhet, és csak betűkből és számokból állhat. Ha nincs megadva, a függvényalkalmazás az alapértelmezett feladat hub neve: **DurableFunctionsHub**. További információkért lásd: [hubs feladat](../articles/azure-functions/durable-functions-task-hubs.md).

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternatív [feladat hub](../articles/azure-functions/durable-functions-task-hubs.md) nevek segítségével elkülönítheti egymástól, több Durable Functions-alkalmazás akkor is, ha az azonos tárolási háttéralkalmazások használatával theyre.|
|ControlQueueBatchSize|32|A vezérlő várólista lekéréshez egyszerre üzenetek száma.|
|PartitionCount |4|A partíciók száma az ellenőrzési várólista. Egy 1 és 16 közötti pozitív egész lehet.|
|ControlQueueVisibilityTimeout |5 perc|A várólistából kivéve vezérlés üzenetsorbeli üzenetek láthatósági időkorlátot.|
|WorkItemQueueVisibilityTimeout |5 perc|A munkahelyi el távolítva a sorból elem üzenetsorbeli üzenetek láthatósági időkorlátot.|
|MaxConcurrentActivityFunctions |10 x az aktuális gépen processzorok száma|Tevékenységfüggvényeket egyetlen gazdagép-példány egy időben feldolgozható maximális számát.|
|MaxConcurrentOrchestratorFunctions |10 x az aktuális gépen processzorok száma|Az orchestrator-függvények, amelyek egyetlen gazdagép-példány egy időben feldolgozható maximális számát.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Az alapul szolgáló Azure Storage-erőforrások kezelésére szolgáló Azure Storage kapcsolati karakterláncát alkalmazásbeállítás neve.|
|TraceInputsAndOutputs |false|E lépések bemeneteit és kimeneteit a függvényhívások nyomkövetési jelző érték beolvasása. Az alapértelmezett viselkedést, ha a nyomkövetés a függvény végrehajtási eseményeket, hogy a szerializált bemeneteit és kimeneteit a függvényhívások felvétel bájtok száma. Ez a lehető legkevesebb információt a bemenetek és kimenetek kinézni a naplók puffadás vagy véletlenül is közzéteheti a naplókba bizalmas információk nélkül biztosít. Ez a tulajdonság true értékre való állítására az alapértelmezett függvény naplózási jelentkezhetnek függvény bemeneti és kimeneti teljes tartalmát.|
|LogReplayEvents|false|Vezénylési visszajátszását beírni az Application Insights-e jelző érték beolvasása.|
|EventGridTopicEndpoint ||A végpont URL-címét egy Azure Event Griddel egyéni témakört. Ha ez a tulajdonság értéke, orchestration életciklusának értesítési esemény közzé lesz téve, ennek a végpontnak. Ez a tulajdonság támogatja az alkalmazásbeállítások megoldás.|
|EventGridKeySettingName ||Az Azure Event Griddel egyéni témakörre, amely a hitelesítéshez használt kulcs tartalmazó alkalmazásbeállítás neve `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Sikertelen lesz, ha az Event Grid-témakör közzétételét újrapróbálkozások száma.|
|EventGridPublishRetryInterval|5 perc|Az Event Grid közzéteszi az újrapróbálkozási időköznek a *óó* formátumban.|

Ezek közül sok teljesítmény optimalizálásához. További információkért lásd: [teljesítmény és méretezhetőség](../articles/azure-functions/durable-functions-perf-and-scale.md).