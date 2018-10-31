---
title: az Azure Functions – Host.JSON referencia 1.x
description: Az Azure Functions host.json fájlt a v1-futtatókörnyezetben dokumentációja.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: ee82aab37973117b0c1960d8b75a29bfad38b7c7
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252165"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>az Azure Functions – Host.JSON referencia 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2. verzió](functions-host-json.md)

A *host.json* metaadatait tartalmazó fájl tartalmaz, amelyek befolyásolják a függvényalkalmazás a függvények globális konfigurációs beállításokat. Ez a cikk érhetők el a v1 futásidejű beállításokat sorolja fel. A JSON-sémájában jelenleg http://json.schemastore.org/host.

> [!NOTE]
> Ez a cikk az Azure Functions 1.x.  Az a funkciók host.json hivatkozás 2.x verzióját, lásd: [Azure Functions – host.json referencia 2.x](functions-host-json.md).

A felügyelt egyéb függvény alkalmazás konfigurációs lehetőségek a [Alkalmazásbeállítások](functions-app-settings.md).

Egyes host.json beállításai csak a helyi futtatásakor használt a [local.settings.json](functions-run-local.md#local-settings-file) fájlt.

## <a name="sample-hostjson-file"></a>Mintafájl host.json

Az alábbi minta *host.json* fájlok a megadott összes lehetséges lehetősége van.


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

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

A konfigurációs beállítások [Event Hub-eseményindítók és kötések](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

A feladat gazdagépen futó függvények listája. Üres tömb azt jelenti, hogy a függvények futtatása. Használatra szánt csak akkor, ha [helyileg futó](functions-run-local.md). Függvény-alkalmazásokban az Azure-ban, ehelyett kövesse a lépéseket a [letiltása az Azure Functions függvények](disable-function.md) ezen beállítás használata helyett adott funkciók letiltása.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Azt jelzi, hogy a függvények az időtúllépési időtartama. A kiszolgáló nélküli Használatalapú csomagok esetében az érvényes értéktartomány: 1 másodperctől 10 percre, és az alapértelmezett érték 5 perc. Az App Service-csomag nem általános korlátozott, és az alapértelmezett futtatókörnyezet verziójának függ.

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
|engedélyezve|true|Itt adhatja meg, hogy engedélyezve van-e a szolgáltatás. | 
|healthCheckInterval|10 másodperc|A háttérben történő rendszeres egészségügyi közötti időintervallum ellenőrzi. | 
|healthCheckWindow|2 perc|Egy változó időablakban együtt használható a `healthCheckThreshold` beállítás.| 
|healthCheckThreshold|6|Az állapot-ellenőrzés maximálisan megengedett számú meghiúsulhat a rendszer kezdeményezi a gazdagép újraindítása előtt.| 
|counterThreshold|0,80|A küszöbérték, amely egy teljesítményszámláló minősülnek nem megfelelő állapotú.| 

## <a name="http"></a>http

A konfigurációs beállítások [http-eseményindítók és kötések](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Verzió csak 1.x.*

Egy feladat gazdagép egyedi azonosítója. Szaggatott vonal a kisbetűs GUID távolíthatja el. Ha helyileg futtatja a szükséges. Ha Azure-ban futó ajánlott-azonosító értéke nincs beállítva. Az azonosító az Azure-ban automatikusan generált amikor `id` van hagyva. 

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

*Verzió 1.x*

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
