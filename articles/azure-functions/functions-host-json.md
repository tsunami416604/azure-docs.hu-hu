---
title: az Azure Functions – Host.JSON referencia 2.x
description: A v2 modul az Azure Functions host.json fájlt dokumentációja.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 4da64f01f3b4f39bd10fd3cb1b67910ffca886b8
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413268"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>az Azure Functions – Host.JSON referencia 2.x  

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2-es verzió](functions-host-json.md)

A *host.json* metaadatait tartalmazó fájl tartalmaz, amelyek befolyásolják a függvényalkalmazás a függvények globális konfigurációs beállításokat. Ez a cikk felsorolja az v2 modul rendelkezésre álló beállításokat.  

> [!NOTE]
> Ez a cikk az Azure Functions 2.x.  Az a funkciók host.json odkaz 1.x, lásd: [Azure Functions – host.json referencia 1.x](functions-host-json-v1.md).

A felügyelt egyéb függvény alkalmazás konfigurációs lehetőségek a [Alkalmazásbeállítások](functions-app-settings.md).

Egyes host.json beállításai csak a helyi futtatásakor használt a [local.settings.json](functions-run-local.md#local-settings-file) fájlt.

## <a name="sample-hostjson-file"></a>Mintafájl host.json

Az alábbi minta *host.json* fájlok a megadott összes lehetséges lehetősége van.


```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

Ez a cikk a következő szakaszok azt ismertetik, hogy minden felső szintű tulajdonságot. Mind a nem kötelező, ha nincs másképp.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Ez a beállítás nem gyermeke [naplózás](#logging).

Szabályozza a [mintavételezés funkció az Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
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

## <a name="cosmosdb"></a>cosmos DB

Konfigurációs beállítás található [Cosmos DB-eseményindítók és kötések](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Konfigurációs beállítás található [Durable Functions-kötéseket](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Konfigurációs beállítások található [Event Hub-eseményindítók és kötések](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>Bővítmények

Tulajdonság által visszaadott olyan objektum, amely tartalmazza az összes a kötés-specifikus beállítások, például [http](#http) és [eventHub](#eventhub).

## <a name="functions"></a>functions

A feladat gazdagépen futó függvények listája. Üres tömb azt jelenti, hogy a függvények futtatása. Használatra szánt csak akkor, ha [helyileg futó](functions-run-local.md). Függvény-alkalmazásokban az Azure-ban, ehelyett kövesse a lépéseket a [letiltása az Azure Functions függvények](disable-function.md) ezen beállítás használata helyett adott funkciók letiltása.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Azt jelzi, hogy a függvények az időtúllépési időtartama. A kiszolgáló nélküli Használatalapú csomagok esetében az érvényes értéktartomány: 1 másodperctől 10 percre, és az alapértelmezett érték 5 perc. Az App Service-csomag nem általános korlátozott, és az alapértelmezett futtatókörnyezet verziójának függ. A verzió 2.x, az alapértelmezett érték egy App Service-csomag érték 30 perc. A verzió van 1.x, *null*, ami azt jelenti, hogy nincs időkorlát.

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

Konfigurációs beállítások található [http-eseményindítók és kötések](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>Naplózás

A függvényalkalmazás, beleértve az Application Insights naplózási viselkedéseit szabályozza.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "applicationInsights": {
        ...
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Határozza meg, milyen szintű fájl naplózás engedélyezve van.  Lehetőségek a következők `never`, `always`, `debugOnly`. |
|LogLevel|n/a|A szűrés az alkalmazás a Functions naplókategória definiáló objektum. Verzió 2.x követi az ASP.NET Core-elrendezés log kategóriához szűrésre. Ez lehetővé teszi az egyes funkciók naplózási szűrő. További információkért lásd: [napló szűrése](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) az ASP.NET Core dokumentációjában. |
|konzol|n/a| A [konzol](#console) naplózási beállításokat. |
|applicationInsights|n/a| A [applicationInsights](#applicationinsights) beállítás. |

## <a name="console"></a>konzol

Ez a beállítás nem gyermeke [naplózás](#logging). Azt szabályozza, hogy a konzolon, ha nem a hibakeresési mód naplózása.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|isEnabled|false|Engedélyezi vagy letiltja a konzol naplózását.| 

## <a name="queues"></a>üzenetsorok

Konfigurációs beállítások található [tárolási üzenetsor eseményindítók és kötések](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>A SendGrid

Konfigurációs beállítás található [SendGrid eseményindítók és kötések](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Konfigurációs beállítás található [Service Bus-eseményindítók és kötések](functions-bindings-service-bus.md#host-json).

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

## <a name="version"></a>version

A verzió-karakterlánc `"version": "2.0"` a v2 futtatókörnyezetre függvényalkalmazás megadása kötelező.

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
