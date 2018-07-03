---
title: az Azure Functions – Host.JSON referencia
description: Az Azure Functions host.json fájl dokumentációja.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: tdykstra
ms.openlocfilehash: d89170f796355b734facc5e08ad1815a2b865d49
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342092"
---
# <a name="hostjson-reference-for-azure-functions"></a>az Azure Functions – Host.JSON referencia

A *host.json* metaadatait tartalmazó fájl tartalmaz, amelyek befolyásolják a függvényalkalmazás a függvények globális konfigurációs beállításokat. Ez a cikk felsorolja az elérhető beállítások. A JSON-sémájában jelenleg http://json.schemastore.org/host.

Egyéb globális konfigurációs lehetőségek [Alkalmazásbeállítások](functions-app-settings.md) és a [local.settings.json](functions-run-local.md#local-settings-file) fájlt.

## <a name="sample-hostjson-file"></a>Mintafájl host.json

Az alábbi minta *host.json* fájl van megadva minden lehetséges beállítást.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Ez a cikk a következő szakaszok azt ismertetik, hogy minden felső szintű tulajdonságot. Mind a nem kötelező, ha nincs másképp.

## <a name="aggregator"></a>aggregator

Megadja, hogy hány függvény meghívásához összesített mikor [kiszámítása a metrikákat az Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Tulajdonság |Alapértelmezett  | Leírás |
|---------|---------|---------| 
|batchSize|1000|Kérelmek maximális számát az összesítést.| 
|flushTimeout|00:00:30|Maximális idő időszak alatt, és összesítést.| 

Összesítjük függvény meghívásához, ha a kettő közül az első korlátozza elérésekor.

## <a name="applicationinsights"></a>applicationInsights

Szabályozza a [mintavételezés funkció az Application Insights](functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|isEnabled|true|Engedélyezi vagy letiltja a mintavétel.| 
|maxTelemetryItemsPerSecond|5|A küszöbértéket, mely mintavételi kezdődik.| 

## <a name="durabletask"></a>durableTask

A konfigurációs beállítások [Durable Functions](durable-functions-overview.md).

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

A feladat értesítésiközpont-nevek kell betűvel kezdődhet, és csak betűkből és számokból állhat. Ha nincs megadva, a függvényalkalmazás az alapértelmezett feladat hub neve: **DurableFunctionsHub**. További információkért lásd: [hubs feladat](durable-functions-task-hubs.md).

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternatív [feladat hub](durable-functions-task-hubs.md) használható neveket, még akkor is, ha az azonos tárolási háttéralkalmazások elkülöníteni egymástól, több Durable Functions-alkalmazás.|
|ControlQueueBatchSize|32|A vezérlő várólista lekéréshez egyszerre üzenetek száma.|
|PartitionCount |4|A partíciók száma az ellenőrzési várólista. Egy 1 és 16 közötti pozitív egész lehet.|
|ControlQueueVisibilityTimeout |5 perc|A várólistából kivéve vezérlés üzenetsorbeli üzenetek láthatósági időkorlátot.|
|WorkItemQueueVisibilityTimeout |5 perc|A munkahelyi el távolítva a sorból elem üzenetsorbeli üzenetek láthatósági időkorlátot.|
|MaxConcurrentActivityFunctions |10 x az aktuális gépen processzorok száma|Tevékenységfüggvényeket egyetlen gazdagép-példány egy időben feldolgozható maximális számát.|
|MaxConcurrentOrchestratorFunctions |10 x az aktuális gépen processzorok száma|Tevékenységfüggvényeket egyetlen gazdagép-példány egy időben feldolgozható maximális számát.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Az alapul szolgáló Azure Storage-erőforrások kezelésére szolgáló Azure Storage kapcsolati karakterláncát alkalmazásbeállítás neve.|
|TraceInputsAndOutputs |false|E lépések bemeneteit és kimeneteit a függvényhívások nyomkövetési jelző érték beolvasása. Az alapértelmezett viselkedést, ha a nyomkövetés a függvény végrehajtási eseményeket, hogy a szerializált bemeneteit és kimeneteit a függvényhívások felvétel bájtok száma. Ez a lehető legkevesebb információt a bemenetek és kimenetek kinézni a naplók puffadás vagy véletlenül is közzéteheti a naplókba bizalmas információk nélkül biztosít. Ez a tulajdonság true értékre való állítására az alapértelmezett függvény naplózási jelentkezhetnek függvény bemeneti és kimeneti teljes tartalmát.|
|LogReplayEvents|false|Vezénylési visszajátszását beírni az Application Insights-e jelző érték beolvasása.|
|EventGridTopicEndpoint ||A végpont URL-címét egy Azure Event Griddel egyéni témakört. Ha ez a tulajdonság értéke, orchestration életciklusának értesítési esemény közzé lesz téve, ennek a végpontnak. Ez a tulajdonság támogatja az alkalmazásbeállítások megoldás.|
|EventGridKeySettingName ||Az Azure Event Griddel egyéni témakörre, amely a hitelesítéshez használt kulcs tartalmazó alkalmazásbeállítás neve `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Sikertelen lesz, ha az Event Grid-témakör közzétételét újrapróbálkozások száma.|
|EventGridPublishRetryInterval|5 perc|Az Event Grid közzététele az újrapróbálkozási időköznek a *óó* formátumban.|

Ezek közül sok teljesítmény optimalizálásához. További információkért lásd: [teljesítmény és méretezhetőség](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

A konfigurációs beállítások [Event Hub-eseményindítók és kötések](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

A feladat gazdagépen futtatandó függvények listája. Üres tömb azt jelenti, hogy a függvények futtatása. Használatra szánt csak akkor, ha [helyileg futó](functions-run-local.md). A függvényalkalmazások, használja a *function.json* `disabled` tulajdonság helyett ezt a tulajdonságot a *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Azt jelzi, hogy a függvények az időtúllépési időtartama. A Használatalapú csomagok az érvényes értéktartomány: 1 másodperctől 10 percre, és az alapértelmezett érték 5 perc. Az App Service-csomagokban nem korlátozott, és az alapértelmezett értéke null, ami azt jelenti, hogy nincs időkorlát.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

A konfigurációs beállítások [gazdagép állapotfigyelőjét](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|engedélyezve|true|A funkció engedélyezve van-e. | 
|healthCheckInterval|10 másodperc|A háttérben történő rendszeres egészségügyi közötti időintervallum ellenőrzi. | 
|healthCheckWindow|2 perc|Egy változó időablakban együtt használható a `healthCheckThreshold` beállítás.| 
|healthCheckThreshold|6|Az állapot-ellenőrzés maximálisan megengedett számú meghiúsulhat a rendszer kezdeményezi a gazdagép újraindítása előtt.| 
|counterThreshold|0,80|A küszöbérték, amely egy teljesítményszámláló minősülnek nem megfelelő állapotú.| 

## <a name="http"></a>http

A konfigurációs beállítások [http-eseményindítók és kötések](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

Egy feladat gazdagép egyedi azonosítója. Szaggatott vonal a kisbetűs GUID távolíthatja el. Ha helyileg futtatja a szükséges. Az Azure Functions futtatásakor azonosító automatikusan generált Ha `id` van hagyva.

Ha több függvényalkalmazás között megosztott tárfiókot, győződjön meg róla, hogy rendelkezik-e egy másik minden függvényalkalmazáshoz `id`. Kihagyhatja a `id` tulajdonságot vagy állítsa be kézzel a minden függvényalkalmazáshoz `id` más értékre. Az időzítő eseményindító egy tárolási zár segítségével győződjön meg arról, hogy lesz időzítő csak egy példánnyal, amikor a függvényalkalmazás elvégzi a horizontális felskálázást több példányra. Ha két függvényalkalmazások azonos `id` és minden egyes egy időzítő indítófeltételt használ, csak egy időzítő fog futni.


```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>naplózó

Vezérlők által írt naplók szűrése egy [ILogger objektum](functions-monitoring.md#write-logs-in-c-functions) vagy [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|categoryFilter|n/a|Meghatározza a Szűrés kategória szerint| 
|defaultLevel|Információ|A nincs megadva a kategóriákat a `categoryLevels` tömböt, ezen a szinten és újabb verziókhoz naplók küldése az Application Insightsba.| 
|categoryLevels|n/a|Kategóriák tömbje, amely meghatározza a küldendő kategóriákhoz tartozó Application Insights a minimális naplózási szint. Az itt megadott kategória szabályozza minden kategória ugyanazzal az értékkel kezdődő, és hosszabb értékek elsőbbséget. A fenti mintában *host.json* fájlt, az összes kategória napló "Host.Aggregator" karakterrel kezdődő `Information` szintjét. Minden más kategóriák kezdődő "Host", "Host.Executor", például log `Error` szintjét.| 

## <a name="queues"></a>üzenetsorok

A konfigurációs beállítások [tárolási üzenetsor eseményindítók és kötések](functions-bindings-storage-queue.md).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

A konfigurációs beállítás [Service Bus-eseményindítók és kötések](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>Egypéldányos

Egyszeres zárolási viselkedés konfigurációs beállításait. További információkért lásd: [egyszeres támogatására vonatkozó GitHub-problémát](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|lockPeriod|00:00:15|Az időszak, amelyeket a függvény szint zárolások rendszer számára. A zárolás automatikus megújítási.| 
|listenerLockPeriod|00:01:00|Az időszak elvégzett figyelő zárolások számára.| 
|listenerLockRecoveryPollingInterval|00:01:00|A figyelő zárolási helyreállításhoz használt, ha egy figyelő nem zárolható indításkor időtartam alatt.| 
|lockAcquisitionTimeout|00:01:00|Legfeljebb ennyi idő a modul megpróbálja zárolni.| 
|lockAcquisitionPollingInterval|n/a|A zárolás adatolvasási kísérletek közötti időköz.| 

## <a name="tracing"></a>nyomkövetés

Konfigurációs beállítások használatával létrehozott naplók egy `TraceWriter` objektum. Lásd: [C# naplózási](functions-reference-csharp.md#logging) és [Node.js naplózási](functions-reference-node.md#writing-trace-output-to-the-console). 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|consoleLevel|információ|A konzol naplózási nyomkövetési szintet. Lehetőségek a következők: `off`, `error`, `warning`, `info`, és `verbose`.|
|fileLoggingMode|debugOnly|A nyomkövetési fájl naplózási szintet. Lehetőségek a következők `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Egy [megosztott kód könyvtárak](functions-reference-csharp.md#watched-directories) , amely kell figyelni a módosításokat.  Biztosítja, hogy ezek a könyvtárak a kód módosításakor a változások mértékének a függvények.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg a host.json fájl frissítése](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Tekintse meg a globális beállítások környezeti változók](functions-app-settings.md)
