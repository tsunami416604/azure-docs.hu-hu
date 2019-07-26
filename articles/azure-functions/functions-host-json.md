---
title: Host. JSON-hivatkozás Azure Functions 2. x rendszerhez
description: A v2 futtatókörnyezettel rendelkező Azure Functions Host. JSON fájl dokumentációja.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: ecb2059e529347b7eff72bf6af74b82558a4c251
ms.sourcegitcommit: 83a89c45253b0d432ce8dcd70084c18e9930b1fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371691"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Host. JSON-hivatkozás Azure Functions 2. x rendszerhez  

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2-es verzió](functions-host-json.md)

A *Host. JSON* metaadat-fájl olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function app összes funkcióját érintik. Ez a cikk a v2 futtatókörnyezethez elérhető beállításokat sorolja fel.  

> [!NOTE]
> Ez a cikk a 2. x Azure Functions.  Az a funkciók host.json odkaz 1.x, lásd: [Azure Functions – host.json referencia 1.x](functions-host-json-v1.md).

Az [alkalmazás beállításaiban](functions-app-settings.md)az egyéb Function app konfigurációs beállításai is kezelhetők.

Néhány gazdagép. JSON-beállítás csak akkor használatos, ha helyileg fut a [Local. Settings. JSON](functions-run-local.md#local-settings-file) fájlban.

## <a name="sample-hostjson-file"></a>Példa Host. JSON fájlra

A következő minta *Host. JSON* fájlokhoz minden lehetséges beállítás van megadva.

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
    "watchDirectories": [ "Shared", "Test" ],
    "managedDependency": {
        "enabled": true
    }
}
```

A cikk következő fejezetei ismertetik az egyes legfelső szintű tulajdonságokat. Ha másként nincs megadva, az összes megadása nem kötelező.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Ez a beállítás a [naplózás](#logging)gyermeke.

A [Application Insights mintavételi funkcióját](./functions-monitoring.md#configure-sampling)vezérli.

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

> [!NOTE]
> A napló mintavételezése miatt előfordulhat, hogy egyes végrehajtások nem jelennek meg a Application Insights figyelő paneljén.

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|isEnabled|true|Engedélyezheti vagy letilthatja a mintavételezést.| 
|maxTelemetryItemsPerSecond|5|A mintavételezés megkezdésének küszöbértéke.| 

## <a name="cosmosdb"></a>cosmosDb

A konfigurációs beállítás [Cosmos db eseményindítókban és kötésekben](functions-bindings-cosmosdb-v2.md#host-json)található.

## <a name="durabletask"></a>durableTask

A konfigurációs beállítás a [Durable functions kötéseiben](durable/durable-functions-bindings.md#host-json)található.

## <a name="eventhub"></a>eventHub

A konfigurációs beállítások az [Event hub-eseményindítókban és](functions-bindings-event-hubs.md#host-json)-kötésekben találhatók. 

## <a name="extensions"></a>Extensions

Tulajdonság, amely egy olyan objektumot ad vissza, amely tartalmazza az összes kötési beállítást, például a [http](#http) -t és a [eventHub](#eventhub).

## <a name="functions"></a>függvény

A gazdagép által futtatott függvények listája. Az üres tömb az összes függvény futtatását jelenti. Csak [helyileg futtatott](functions-run-local.md)használatra készült. Az Azure-ban a Function apps szolgáltatásban a [függvények letiltása](disable-function.md) a Azure Functionsben című cikkben ismertetett lépéseket követve letilthatja az egyes függvényeket, és nem használhatja ezt a beállítást.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Az összes függvény időtúllépési időtartamát jelzi. A kiszolgáló nélküli fogyasztási csomag esetében az érvényes tartomány 1 másodperc és 10 perc között van, az alapértelmezett érték pedig 5 perc. Egy App Service tervben nincs általános korlát, és az alapértelmezett érték a futásidejű verziótól függ. A 2. x verzióban egy App Service csomag alapértelmezett értéke 30 perc. Az 1. x verzióban az *értéke null*, amely nem jelez időtúllépést. Nem állítható be végtelenként. Ha nem kifejezetten ezt az értéket állítja be, akkor az alapértelmezett 30 perces értéket fogja megtenni.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

A [gazdagép állapotának figyelésére](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)vonatkozó konfigurációs beállítások.

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
|enabled|true|Megadja, hogy engedélyezve van-e a szolgáltatás. | 
|healthCheckInterval|10 másodperc|Az időszakos háttér állapotának ellenőrzése közötti időtartam. | 
|healthCheckWindow|2 perc|A `healthCheckThreshold` beállítással együtt használt csúszó Time-ablak.| 
|healthCheckThreshold|6|Az állapot-ellenőrzések maximális száma a gazdagép újraindítása előtt.| 
|counterThreshold|0,80|Az a küszöbérték, amelynél a teljesítményszámláló a nem megfelelő állapotot veszi figyelembe.| 

## <a name="http"></a>http

A konfigurációs beállítások a http- [Eseményindítók és](functions-bindings-http-webhook.md)-kötések szolgáltatásban találhatók.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>naplózás

A Function alkalmazás naplózási viselkedését szabályozza, beleértve a Application Insightst is.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Meghatározza, hogy a fájlok naplózása milyen szintű legyen engedélyezve.  A lehetőségek `never`a `always` következők`debugOnly`:,. |
|logLevel|n/a|Az alkalmazásban lévő függvények naplózási kategóriájának szűrését meghatározó objektum. A 2. x verzió a naplózási kategória szűrésének ASP.NET Core elrendezését követi. Ez lehetővé teszi adott függvények naplózásának szűrését. További információ: naplózási [szűrés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) a ASP.net Core dokumentációjában. |
|console|n/a| A [console](#console) naplózási beállításokat. |
|applicationInsights|n/a| A [applicationInsights](#applicationinsights) beállítás. |

## <a name="console"></a>console

Ez a beállítás a [naplózás](#logging)gyermeke. A konzol naplózását vezérli, ha nem hibakeresési módban van.

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
|isEnabled|false|Engedélyezheti vagy letilthatja a konzol naplózását.| 

## <a name="queues"></a>üzenetsorok

A konfigurációs beállítások a [Storage üzenetsor-eseményindítók és](functions-bindings-storage-queue.md#host-json)-kötések szolgáltatásban találhatók.  

## <a name="sendgrid"></a>sendGrid

A konfigurációs beállítás a SendGrid- [Eseményindítók és-kötések](functions-bindings-sendgrid.md#host-json)szolgáltatásban található.

## <a name="servicebus"></a>serviceBus

A konfigurációs beállítás [Service Bus eseményindítókban és kötésekben](functions-bindings-service-bus.md#host-json)található.

## <a name="singleton"></a>Singleton

Az egyszeri zárolási viselkedés konfigurációs beállításai. További információért lásd az egyszeri [támogatással kapcsolatos GitHub-problémát](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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
|lockPeriod|00:00:15|Az az időszak, ameddig a rendszer a működési szintet zárolja. A zárolások automatikus megújítása.| 
|listenerLockPeriod|00:01:00|A figyelő zárolásának időtartama.| 
|listenerLockRecoveryPollingInterval|00:01:00|A figyelő zárolásának helyreállításához használt időintervallum, ha a figyelő zárolása nem szerezhető be indításkor.| 
|lockAcquisitionTimeout|00:01:00|Az a maximális időtartam, ameddig a futtatókörnyezet megpróbál zárolást benyerni.| 
|lockAcquisitionPollingInterval|n/a|A zárolási beszerzési kísérletek közötti időköz.| 

## <a name="version"></a>version

A v2 futtatókörnyezetet megcélzó Function alkalmazás verziójának karakterláncát `"version": "2.0"` kötelező megadni.

## <a name="watchdirectories"></a>watchDirectories

A módosításokat figyelő [megosztott kód-címtárak](functions-reference-csharp.md#watched-directories) készlete.  Gondoskodik arról, hogy a könyvtárakban lévő kódok változásakor a függvények a módosításokat is felveszik.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

A felügyelt függőség egy előzetes verziójú funkció, amely jelenleg csak a PowerShell-alapú függvények esetében támogatott. Lehetővé teszi a függőségek automatikus kezelését a szolgáltatással. Ha az enabled tulajdonság értéke TRUE (igaz), a rendszer feldolgozza a [követelmények. psd1](functions-reference-powershell.md#dependency-management) fájlt. A függőségek akkor frissülnek, ha minden másodlagos verzió megjelent.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató a Host. JSON fájl frissítéséhez](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Lásd: globális beállítások a környezeti változókban](functions-app-settings.md)
