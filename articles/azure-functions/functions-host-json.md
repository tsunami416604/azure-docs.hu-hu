---
title: az Azure Functions Host.JSON referenciája
description: Az Azure Functions host.json fájl dokumentációját.
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
ms.openlocfilehash: d1dec6f2da4f6fcbeb38585fc6a1cfcd9d622c4a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764587"
---
# <a name="hostjson-reference-for-azure-functions"></a>az Azure Functions Host.JSON referenciája

A *host.json* metaadatait tartalmazó fájl tartalmazza, amelyek hatással vannak az összes funkciójának függvény alkalmazások globális konfigurációs beállításokat. Ez a cikk a rendelkezésre álló beállításokat sorolja fel. A JSON-séma jelenleg http://json.schemastore.org/host.

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

Ez a cikk az alábbi szakaszok azt ismertetik, hogy minden legfelső szintű tulajdonság. Az összes opcionálisak hiányában.

## <a name="aggregator"></a>aggregator

Meghatározza, hogy hány függvény meghívásához kell összesíteni mikor [kiszámítása metrikákat az Application Insights](functions-monitoring.md#configure-the-aggregator). 

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
|IsEnabled|true|Engedélyezheti vagy letilthatja a mintavétel.| 
|maxTelemetryItemsPerSecond|5|A küszöbérték, mely mintavételi kezdődik.| 

## <a name="durabletask"></a>durableTask

Konfigurációs beállításainak [tartós funkciók](durable-functions-overview.md).

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
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey"
  }
}
```

Feladatnév hub kell betűvel kezdődhet, és csak betűkből és számokból állhat. Ha nincs megadva, alapértelmezés szerint a feladat hub függvény alkalmazások ez **DurableFunctionsHub**. További információkért lásd: [hubok feladat](durable-functions-task-hubs.md).

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternatív [feladat hub](durable-functions-task-hubs.md) nevei különítheti el egymástól, több tartós funkciók alkalmazások használhatók, még akkor is, ha a ugyanazon tároló háttér használják.|
|ControlQueueBatchSize|32|A vezérlő várólistában egyszerre lekéréses üzenetek száma.|
|PartitionCount |4|A vezérlő várólista partíciók száma. Egy 1 és 16 közötti pozitív egész lehet.|
|ControlQueueVisibilityTimeout |5 perc|A várólista-üzenetek várólistából kivéve vezérlő láthatósági időkorlátot.|
|WorkItemQueueVisibilityTimeout |5 perc|A cikk üzenetek várólistából kivéve munkahelyi láthatósági időkorlátot.|
|MaxConcurrentActivityFunctions |10 x az aktuális gép processzorainak száma|Az egyszerre feldolgozandó egyetlen példányán tevékenység funkciók maximális száma.|
|MaxConcurrentOrchestratorFunctions |10 x az aktuális gép processzorainak száma|Az egyszerre feldolgozandó egyetlen példányán tevékenység funkciók maximális száma.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Az Alkalmazásbeállítás, amely rendelkezik az alapul szolgáló Azure Storage-erőforrások kezeléséhez használt Azure Storage kapcsolati karakterlánc neve.|
|TraceInputsAndOutputs |false|Egy érték a be- és kimenetekkel a függvényhívások nyomon követéséhez. Az alapértelmezett viselkedést, ha a függvény végrehajtása események nyomkövetés bájtok száma szerepeljenek a szerializált be- és kimenetekkel a függvényhívások. Ez hogyan bemenetekhez és kimenetekhez meg a naplók puffadás vagy véletlenül teszi ki a naplókat a bizalmas információk nélkül minimális információkat biztosít. Ez a tulajdonság true értékre való állítására az alapértelmezett függvény naplózási bejelentkezési függvény bemenetekhez és kimenetekhez teljes tartalmát.|
|EventGridTopicEndpoint ||Egy Azure esemény rács egyéni témakör végpont URL-CÍMÉT. Ha ez a tulajdonság értéke, ehhez a végponthoz vezénylési életciklus értesítési események kerülnek közzétételre.|
|EventGridKeySettingName ||Az Azure Event rács egyéni témakört a hitelesítéséhez szeretne használni a kulcsot tartalmazó alkalmazásbeállítás neve `EventGridTopicEndpoint`.

Ezek számos megfelelően teljesítményének optimalizálásához. További információkért lásd: [teljesítmény és méretezhetőség](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Konfigurációs beállításainak [Eseményközpont eseményindítók és kötések](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

A feladat gazdagépen futtatandó funkciók listáját. Üres tömb azt jelenti, hogy minden függvények futtatása. Készült, csak ha [helyileg futó](functions-run-local.md). Függvény alkalmazásokban, használja a *function.json* `disabled` helyett ezt a tulajdonságot a *host.json*.

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

## <a name="healthmonitor"></a>healthMonitor

Konfigurációs beállításainak [állomás figyelő](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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
|engedélyezve|true|Hogy az engedélyezve van. | 
|healthCheckInterval|10 másodperc|A háttérben történő rendszeres állapotfigyelő közötti időközt ellenőrzi. | 
|healthCheckWindow|2 perc|Egy alkalommal csúszóablak együtt használható a `healthCheckThreshold` beállítást.| 
|healthCheckThreshold|6|Az állapot-ellenőrzéssel maximálisan megengedett számú meghiúsulhatnak előtt állomás újrahasznosítást lehet kezdeményezni.| 
|counterThreshold|0,80|A küszöbérték, amelynél a teljesítményszámláló akkor veszi figyelembe a nem megfelelő.| 

## <a name="http"></a>http

Konfigurációs beállításainak [http eseményindítók és kötések](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

Egy feladat állomás egyedi azonosítója. A kötőjelek kisbetű GUID távolíthatja el. A helyi futtatás során szükséges. Az Azure Functions futtatásakor egy hozza létre automatikusan Ha `id` meg van adva.

Ha több függvény alkalmazások között megosztott tárfiókot, győződjön meg arról, hogy minden függvény alkalmazás rendelkezik egy másik `id`. Akkor kihagyhatja a `id` tulajdonság, vagy manuálisan állítsa be az egyes függvény alkalmazások `id` más értékre. Az időzítő indítófeltételt tárolási zárolást használatával gondoskodjon arról, hogy csak egy időzítő példány amikor egy függvény app méretezi ki több példányára. Ha két függvény alkalmazások azonos `id` és egy időzítő indítófeltételt használó minden egyes, csak egy időzítő fog futni.


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

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Konfigurációs beállítást [Service Bus-eseményindítók és kötések](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>Egypéldányos

Egypéldányos zárolási viselkedés konfigurációs beállításait. További információkért lásd: [GitHub probléma egypéldányos támogatásával kapcsolatos](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: a host.json fájl frissítése](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [A környezeti változók globális beállításai](functions-app-settings.md)
