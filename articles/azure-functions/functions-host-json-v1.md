---
title: a Azure Functions 1. x gazdagép. JSON-referenciája
description: A Azure Functions Host. JSON fájl dokumentációja a v1 futtatókörnyezettel.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 3d6a28c8cdcf13dc805d70832ed65732911138cd
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263344"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>a Azure Functions 1. x gazdagép. JSON-referenciája

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2-es verzió](functions-host-json.md)

A *Host. JSON* metaadat-fájl olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function app összes funkcióját érintik. Ez a cikk a v1 futtatókörnyezethez elérhető beállításokat sorolja fel. A JSON-séma a következő helyen található: http://json.schemastore.org/host.

> [!NOTE]
> Ez a cikk az 1. x Azure Functions.  A Host. JSON fájl hivatkozása a 2. x függvényben: [Host. JSON-hivatkozás Azure functions 2. x](functions-host-json.md).

Az [alkalmazás beállításaiban](functions-app-settings.md)az egyéb Function app konfigurációs beállításai is kezelhetők.

Néhány gazdagép. JSON-beállítás csak akkor használatos, ha helyileg fut a [Local. Settings. JSON](functions-run-local.md#local-settings-file) fájlban.

## <a name="sample-hostjson-file"></a>Példa Host. JSON fájlra

A következő minta *Host. JSON* fájlokhoz minden lehetséges beállítás van megadva.


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
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
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
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
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

A cikk következő fejezetei ismertetik az egyes legfelső szintű tulajdonságokat. Ha másként nincs megadva, az összes megadása nem kötelező.

## <a name="aggregator"></a>aggregátor

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>Dokumentum-adatbázis

A [Azure Cosmos db trigger és kötések](functions-bindings-cosmosdb.md)konfigurációs beállításai.

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|GatewayMode|Átjáró|A függvény által a Azure Cosmos DB szolgáltatáshoz való csatlakozáskor használt kapcsolati mód. A lehetőségek a következők: `Direct` és `Gateway`|
|Protocol (Protokoll)|https|A függvény által a Azure Cosmos DB szolgáltatáshoz való kapcsolódáskor használt kapcsolati protokoll.  A [két mód magyarázata itt](../cosmos-db/performance-tips.md#networking) olvasható|
|leasePrefix|–|Az alkalmazás összes függvényében használandó bérlet-előtag.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>EventHub

Az [Event hub-eseményindítók és-kötések](functions-bindings-event-hubs.md)konfigurációs beállításai.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

A gazdagép által futtatott függvények listája. Az üres tömb az összes függvény futtatását jelenti. Csak [helyileg futtatott](functions-run-local.md)használatra készült. Az Azure-ban a Function apps szolgáltatásban a [függvények letiltása a Azure Functionsben](disable-function.md) című cikkben ismertetett lépéseket követve letilthatja az egyes függvényeket, és nem használhatja ezt a beállítást.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Az összes függvény időtúllépési időtartamát jelzi. A kiszolgáló nélküli fogyasztási csomag esetében az érvényes tartomány 1 másodperc és 10 perc között van, az alapértelmezett érték pedig 5 perc. Egy App Service tervben nincs általános korlát, és az alapértelmezett érték a futásidejű verziótól függ.

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
|Engedélyezve|igaz|Megadja, hogy engedélyezve van-e a szolgáltatás. | 
|healthCheckInterval|10 másodperc|Az időszakos háttér állapotának ellenőrzése közötti időtartam. | 
|healthCheckWindow|2 perc|A `healthCheckThreshold` beállítással együtt használt csúszó Time-ablak.| 
|healthCheckThreshold|6|Az állapot-ellenőrzések maximális száma a gazdagép újraindítása előtt.| 
|counterThreshold|0,80|Az a küszöbérték, amelynél a teljesítményszámláló a nem megfelelő állapotot veszi figyelembe.| 

## <a name="http"></a>http

[Http-eseményindítók és-kötések](functions-bindings-http-webhook.md)konfigurációs beállításai.

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Csak 1. x verzió.*

A feladatok gazdagépének egyedi azonosítója. A kötőjelekkel ellátott kisbetűs GUID lehet. Helyi futtatáskor szükséges. Ha az Azure-ban fut, javasoljuk, hogy ne állítson be azonosító értéket. Az Azure-ban automatikusan létrejön egy azonosító, ha a `id` nincs megadva. 

Ha több Function-alkalmazás között oszt meg egy Storage-fiókot, győződjön meg arról, hogy minden Function alkalmazásnak van más `id`. Kihagyhatja a `id` tulajdonságot, vagy manuálisan is beállíthatja az egyes functions-alkalmazások `id` értékét egy másik értékre. Az időzítő-trigger egy tárolási zárolást használ annak biztosítására, hogy csak egy időzítő példány legyen, ha egy függvény alkalmazás több példányra is méretezhető. Ha két Function-alkalmazás ugyanazt a `id` értéket használja, és mindegyik időzítő-triggert használ, csak egy időzítő fog futni.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Tuskózó

A [ILogger objektum](functions-monitoring.md#write-logs-in-c-functions) vagy a [Context. log](functions-monitoring.md#write-logs-in-javascript-functions)által írt naplók szűrését vezérli.

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
|categoryFilter|–|Meghatározza a kategória szerinti szűrést| 
|defaultLevel|Információ|A `categoryLevels` tömbben nem megadott kategóriák esetében a naplók ezen a szinten és a fentiekben Application Insights.| 
|categoryLevels|–|Kategóriákból álló tömb, amely meghatározza, hogy az egyes kategóriákhoz Application Insights milyen minimális naplózási szint legyen elküldve. Az itt megadott kategória az összes olyan kategóriát szabályozza, amely ugyanazzal az értékkel kezdődik, és a hosszabb értékek elsőbbséget élveznek. Az előző példában szereplő *Host. JSON* fájlban az összes kategória, amely a "host. aggregator" kifejezéssel kezdődik, `Information` szinten. Az összes többi olyan kategória, amely a "host" kifejezéssel kezdődik, például "host. végrehajtó", jelentkezzen be `Error` szinten.| 

## <a name="queues"></a>üzenetsorok

A [tárolási várólista-eseményindítók és-kötések](functions-bindings-storage-queue.md)konfigurációs beállításai.

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
|maxPollingInterval|60000|A várólista-lekérdezések közötti maximális időköz ezredmásodpercben.| 
|visibilityTimeout|0|Az újrapróbálkozások között eltelt idő az üzenet feldolgozásakor.| 
|batchSize|16|Azoknak a üzenetsor-üzeneteknek a száma, amelyeket a függvények futtatókörnyezete egyszerre kér le, és párhuzamosan dolgozza fel a folyamatokat. Ha a feldolgozandó szám a `newBatchThreshold` értékre kerül, a futtatókörnyezet egy másik köteget kap, és elindítja az üzenetek feldolgozását. Így a függvények által feldolgozott egyidejű üzenetek maximális száma @no__t – 0, `newBatchThreshold`. Ez a korlát külön vonatkozik az egyes üzenetsor-vezérelt függvényekre. <br><br>Ha el szeretné kerülni az egyik várólistán fogadott üzenetek párhuzamos végrehajtását, beállíthatja `batchSize` és 1 közötti értéket. Ez a beállítás azonban csak akkor teszi feleslegessé a párhuzamosságot, ha a Function alkalmazás egyetlen virtuális gépen fut (VM). Ha a Function alkalmazás több virtuális gépre is kiterjed, minden egyes virtuális gép futtathatja az egyes üzenetsor által aktivált függvények egy példányát.<br><br>A maximális @no__t – 0 32. | 
|maxDequeueCount|5|Azon alkalmak száma, amelyekkel az üzenetek feldolgozására kerül sor, mielőtt a rendszer áthelyezi azt a Megmérgező várólistára.| 
|newBatchThreshold|batchSize/2|Ha az egyidejűleg feldolgozható üzenetek száma leállítja ezt a számot, a futtatókörnyezet egy másik köteget kérdez le.| 

## <a name="sendgrid"></a>SendGrid

A [SendGrind kimeneti kötésének](functions-bindings-sendgrid.md) konfigurációs beállítása

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|a|–|A küldő e-mail-címe az összes függvényen belül.| 

## <a name="servicebus"></a>ServiceBus

[Service Bus eseményindítók és kötések](functions-bindings-service-bus.md)konfigurációs beállítása.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxConcurrentCalls|16|Az üzenet-szivattyú által kezdeményezett visszahívás egyidejű hívásának maximális száma. Alapértelmezés szerint a függvények futtatókörnyezete egyszerre több üzenetet dolgoz fel. Ha úgy szeretné irányítani a futtatókörnyezetet, hogy egyszerre csak egyetlen üzenetsor vagy témakörbeli üzenetet dolgozzon fel, állítsa `maxConcurrentCalls` és 1 közötti értéket. | 
|prefetchCount|–|Az alapul szolgáló MessageReceiver által használt alapértelmezett PrefetchCount.| 
|autoRenewTimeout|00:05:00|Az a maximális időtartam, amelyen belül az üzenet zárolása automatikusan meg lesz újítva.| 

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
|lockAcquisitionPollingInterval|–|A zárolási beszerzési kísérletek közötti időköz.| 

## <a name="tracing"></a>nyomkövetés

*1. x verzió*

A `TraceWriter` objektum használatával létrehozott naplók konfigurációs beállításai. Lásd: [ C# naplózás](functions-reference-csharp.md#logging) és [Node. js-naplózás](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consoleLevel|információ|A konzol naplózásának nyomkövetési szintje. A lehetőségek a következők: `off`, `error`, `warning`, `info` és `verbose`.|
|fileLoggingMode|debugOnly|A fájlok naplózásának nyomkövetési szintje. A lehetőségek a következők: `never`, `always`, `debugOnly`.| 

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
