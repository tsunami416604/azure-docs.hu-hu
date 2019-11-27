---
title: Host. JSON-hivatkozás Azure Functions 2. x rendszerhez
description: A v2 futtatókörnyezettel rendelkező Azure Functions Host. JSON fájl dokumentációja.
ms.topic: conceptual
ms.date: 09/08/2018
ms.openlocfilehash: bb10f15db1d152ff1d8fd8d38ba22e312a2031b7
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323080"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Host. JSON-hivatkozás Azure Functions 2. x rendszerhez  

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2-es verzió](functions-host-json.md)

A *Host. JSON* metaadat-fájl olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function app összes funkcióját érintik. Ez a cikk a v2 futtatókörnyezethez elérhető beállításokat sorolja fel.  

> [!NOTE]
> Ez a cikk a 2. x Azure Functions.  Az 1. x függvények Host. JSON fájljának hivatkozását lásd: [Host. JSON-dokumentáció Azure functions 1. x-hez](functions-host-json-v1.md).

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
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
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
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> A napló mintavételezése miatt előfordulhat, hogy egyes végrehajtások nem jelennek meg a Application Insights figyelő paneljén.

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|isEnabled|true|Engedélyezheti vagy letilthatja a mintavételezést.| 
|maxTelemetryItemsPerSecond|20|A mintavételezés megkezdésének küszöbértéke.| 
|EnableLiveMetrics |true|Élő metrikák gyűjtésének engedélyezése.|
|EnableDependencyTracking|true|A függőségek követésének engedélyezése.|
|EnablePerformanceCountersCollection|true|Engedélyezi a kudu teljesítményszámlálók gyűjtését.|

## <a name="cosmosdb"></a>cosmosDb

A konfigurációs beállítás [Cosmos db eseményindítókban és kötésekben](functions-bindings-cosmosdb-v2.md#host-json)található.

## <a name="durabletask"></a>durableTask

A konfigurációs beállítás a [Durable functions kötéseiben](durable/durable-functions-bindings.md#host-json)található.

## <a name="eventhub"></a>eventHub

A konfigurációs beállítások az [Event hub-eseményindítókban és-kötésekben](functions-bindings-event-hubs.md#host-json)találhatók. 

## <a name="extensions"></a>Extensions

Tulajdonság, amely egy olyan objektumot ad vissza, amely tartalmazza az összes kötési beállítást, például a [http](#http) -t és a [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

A bővítmények lehetővé teszi, hogy a Function-alkalmazáshoz egy kompatibilis functions-bővítményt adjon hozzá. További információ: [bővítmények a helyi fejlesztéshez](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

A gazdagép által futtatott függvények listája. Az üres tömb az összes függvény futtatását jelenti. Csak [helyileg futtatott](functions-run-local.md)használatra készült. Az Azure-ban a Function apps szolgáltatásban a [függvények letiltása a Azure Functionsben](disable-function.md) című cikkben ismertetett lépéseket követve letilthatja az egyes függvényeket, és nem használhatja ezt a beállítást.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Az összes függvény időtúllépési időtartamát jelzi. A TimeSpan karakterlánc-formátumát követi. A kiszolgáló nélküli fogyasztási csomag esetében az érvényes tartomány 1 másodperc és 10 perc között van, az alapértelmezett érték pedig 5 perc.  

A Prémium csomag esetében az érvényes tartomány 1 másodperc és 60 perc közötti, az alapértelmezett érték pedig 30 perc.

A dedikált (App Service) csomagban nincs általános korlát, és az alapértelmezett érték 30 perc. A `-1` értéke nem kötött végrehajtást jelez, de a rögzített felső korlát fenntartása ajánlott.

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

A konfigurációs beállítások a http- [Eseményindítók és-kötések](functions-bindings-http-webhook.md#hostjson-settings)szolgáltatásban találhatók.

## <a name="logging"></a>naplózás

A Function alkalmazás naplózási viselkedését szabályozza, beleértve a Application Insightst is.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
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
|fileLoggingMode|debugOnly|Meghatározza, hogy a fájlok naplózása milyen szintű legyen engedélyezve.  A lehetőségek a következők: `never`, `always`, `debugOnly`. |
|logLevel|n/a|Az alkalmazásban lévő függvények naplózási kategóriájának szűrését meghatározó objektum. A 2. x verzió a naplózási kategória szűrésének ASP.NET Core elrendezését követi. Ez lehetővé teszi adott függvények naplózásának szűrését. További információ: [naplózási szűrés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) a ASP.net Core dokumentációjában. |
|konzol|n/a| A [konzol](#console) naplózási beállítása. |
|applicationInsights|n/a| A [applicationInsights](#applicationinsights) beállítás. |

## <a name="console"></a>konzol

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

## <a name="manageddependency"></a>managedDependency

A felügyelt függőség egy olyan szolgáltatás, amely jelenleg csak PowerShell-alapú függvények esetén támogatott. Lehetővé teszi a függőségek automatikus kezelését a szolgáltatással. Ha a `enabled` tulajdonság értéke `true`, a rendszer feldolgozza a `requirements.psd1` fájlt. A függőségek akkor frissülnek, ha minden másodlagos verzió megjelent. További információ: [felügyelt függőség](functions-reference-powershell.md#dependency-management) a PowerShell-cikkben.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>üzenetsorok

A konfigurációs beállítások a [Storage üzenetsor-eseményindítók és-kötések](functions-bindings-storage-queue.md#host-json)szolgáltatásban találhatók.  

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

A v2-es futtatókörnyezetet célzó Function alkalmazás esetében a Version string `"version": "2.0"` szükséges.

## <a name="watchdirectories"></a>watchDirectories

A módosításokat figyelő [megosztott kód-címtárak](functions-reference-csharp.md#watched-directories) készlete.  Gondoskodik arról, hogy a könyvtárakban lévő kódok változásakor a függvények a módosításokat is felveszik.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató a Host. JSON fájl frissítéséhez](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Lásd: globális beállítások a környezeti változókban](functions-app-settings.md)
