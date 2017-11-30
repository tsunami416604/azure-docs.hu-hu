---
title: "az Azure Functions Host.JSON referenciája"
description: "Az Azure Functions host.json fájl dokumentációját."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2017
ms.author: tdykstra
ms.openlocfilehash: 522d0590595b0fc0fef503599f1677658f223bd8
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="hostjson-reference-for-azure-functions"></a>az Azure Functions Host.JSON referenciája

A *host.json* metaadatait tartalmazó fájl tartalmazza, amelyek hatással vannak az összes funkciójának függvény alkalmazások globális konfigurációs beállításokat. Ez a cikk a rendelkezésre álló beállításokat sorolja fel. A JSON-séma http://json.schemastore.org/host jelenleg.

A más globális konfigurációs beállítások [Alkalmazásbeállítások](functions-app-settings.md) és a a [local.settings.json](functions-run-local.md#local-settings-file) fájlt.

## <a name="sample-hostjson-file"></a>Mintafájl host.json

Az alábbi minta *host.json* fájl van megadva az összes lehetséges beállítások.

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

Ez a cikk az alábbi szakaszok azt ismertetik, hogy minden legfelső szintű tulajdonság. Az összes opcionálisak hiányában.

## <a name="aggregator"></a>gyűjtő

Meghatározza, hogy hány függvény meghívásához kell összesíteni mikor [kiszámítása metrikákat az Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|batchSize|1000|Kérelmek maximális számát az összesítést.| 
|flushTimeout|00:00:30|Maximális idő összesítendő időszak.| 

Függvény meghívásához összesítése ha korlátozza az első kettő eléri.

## <a name="applicationinsights"></a>applicationInsights

Szabályozza a [Application Insights szolgáltatással mintavételi](functions-monitoring.md#configure-sampling).

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
|IsEnabled|hamis|Engedélyezheti vagy letilthatja a mintavétel.| 
|maxTelemetryItemsPerSecond|5|A küszöbérték, mely mintavételi kezdődik.| 

## <a name="eventhub"></a>Az EventHub

Konfigurációs beállításainak [Eseményközpont eseményindítók és kötések](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>függvény

A feladat gazdagépen futtatandó funkciók listáját.  Üres tömb azt jelenti, hogy minden függvények futtatása.  Készült, csak ha [helyileg futó](functions-run-local.md). Függvény alkalmazásokban, használja a *function.json* `disabled` helyett ezt a tulajdonságot a *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Azt jelzi, hogy az időkorlát tartama az összes funkciót. A felhasználási csomagokban az érvényes értéktartomány: 1 másodperctől 10 percre, és az alapértelmezett érték 5 perc. Az App Service csomagokban nincs korlátozva, és az alapértelmezett értéke null, ami azt jelenti, hogy nincs időtúllépés.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="http"></a>http

Konfigurációs beállításainak [http eseményindítók és kötések](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

Egy feladat állomás egyedi azonosítója. A kötőjelek kisbetű GUID távolíthatja el. A helyi futtatás során szükséges. Az Azure Functions futtatásakor egy hozza létre automatikusan Ha `id` meg van adva.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Naplózó

Vezérlők szerint naplók szűrése egy [ILogger objektum](functions-monitoring.md#write-logs-in-c-functions) vagy [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

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
|defaultLevel|Információ|A nincs megadva a kategóriák a `categoryLevels` tömb, az Application Insights elküldeni a naplókat, ezen a szinten és újabb verziók.| 
|categoryLevels|n/a|Kategóriák tömbje, amely meghatározza a küldendő Application Insights kategóriákhoz tartozó a minimális naplózási szintet. Az itt megadott kategória szabályozza kategóriából egyező értékkel kezdődő, és hosszabb értékek elsőbbséget. Az előző példa *host.json* fájlt, kategóriából naplójának "Host.Aggregator" karakterrel kezdődő `Information` szintjét. Minden más kategóriák "Állomás", például a "Host.Executor" karakterrel kezdődő jelentkezhet `Error` szintjét.| 

## <a name="queues"></a>A várólisták

Konfigurációs beállításainak [tárolási eseményindítók és kötések](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxPollingInterval|60000|A maximális ezredmásodpercben várólista lekérdezések között.| 
|visibilityTimeout|0|Egy üzenet feldolgozása közben az újrapróbálkozások közötti időközt sikertelen lesz.| 
|batchSize|16|Az üzenetsor-üzeneteket beolvasni és párhuzamos feldolgozását száma. A maximális érték a 32.| 
|maxDequeueCount|5|A rendszer hányszor próbálkozzon, egy üzenet feldolgozása előtt helyezze át a poison várólista száma.| 
|newBatchThreshold|batchSize/2|A küszöbérték, ahol az üzenetek új kötegelt beolvasott.| 

## <a name="servicebus"></a>A Szolgáltatásbusz

Konfigurációs beállítást [Service Bus-eseményindítók és kötések](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>Egypéldányos

Egypéldányos zárolási viselkedés konfigurációs beállításait. További információkért lásd: [GitHub probléma egypéldányos támogatásával kapcsolatos](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
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
|lockPeriod|00:00:15|Az időtartamot, ameddig függvény szintű zárolások tegyenek. A zárolás automatikus megújítás.| 
|listenerLockPeriod|00:01:00|Az időtartamot, ameddig a figyelő zárolások tegyenek.| 
|listenerLockRecoveryPollingInterval|00:01:00|A figyelő zárolás helyreállításának használatos, ha egy figyelő nem zárolható indításkor időtartam alatt.| 
|lockAcquisitionTimeout|00:01:00|A maximális időt a futtatókörnyezet megpróbál zárolni.| 
|lockAcquisitionPollingInterval|n/a|Zárolás megszerzésére tett kísérletek közötti időközt.| 

## <a name="tracing"></a>nyomkövetés

A naplófájlokat, amelyek használatával hoz létre beállításait egy `TraceWriter` objektum. Lásd: [C# naplózási](functions-reference-csharp.md#logging) és [Node.js naplózási](functions-reference-node.md#writing-trace-output-to-the-console). 

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
|consoleLevel|információ|A konzol naplózási nyomkövetés szintje. A következők: `off`, `error`, `warning`, `info`, és `verbose`.|
|fileLoggingMode|debugOnly|A fájl naplózási nyomkövetés szintje. Beállítások `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Egy [megosztott kód könyvtárak](functions-reference-csharp.md#watched-directories) , meg kell figyelni a módosításokat.  Biztosítja, hogy ha ezeket a könyvtárakat kód megváltozott, a módosítások átveszik a függvény által.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="durabletask"></a>durableTask

[A feladat hub](durable-functions-task-hubs.md) nevet [tartós funkciók](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Feladatnév hub kell betűvel kezdődhet, és csak betűkből és számokból állhat. Ha nincs megadva, alapértelmezés szerint a feladat hub függvény alkalmazások ez **DurableFunctionsHub**. További információkért lásd: [hubok feladat](durable-functions-task-hubs.md).


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: a host.json fájl frissítése](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [A környezeti változók globális beállításai](functions-app-settings.md)
