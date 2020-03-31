---
title: host.json hivatkozás az Azure Functions 1.x-hez
description: Az Azure Functions host.json fájl referenciadokumentációja a v1-es futásidejű.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 2b00e2343e0959e07b195e2e98c6719a1893b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277049"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host.json hivatkozás az Azure Functions 1.x-hez

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Functions futásidejű verzióját: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2-es verzió](functions-host-json.md)

A *host.json* metaadatfájl olyan globális konfigurációs beállításokat tartalmaz, amelyek hatással vannak egy függvényalkalmazás összes függvényére. Ez a cikk a v1-es futásidejű beállításokat sorolja fel. A JSON-séma http://json.schemastore.org/hosta helyen található.

> [!NOTE]
> Ez a cikk az Azure Functions 1.x.  A host.json a Functions 2.x és újabb függvényekben az [Azure Functions 2.x host.json-referencia című témakörében](functions-host-json.md)található.

A függvényalkalmazás egyéb konfigurációs beállításait az [alkalmazás beállításai](functions-app-settings.md)kezelik.

Egyes host.json-beállítások csak akkor használatosak, ha a [local.settings.json](functions-run-local.md#local-settings-file) fájlban helyileg futnak.

## <a name="sample-hostjson-file"></a>Minta állomás.json fájl

A következő minta *host.json* fájlok minden lehetséges beállítással rendelkeznek.


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

A cikk következő szakaszai ismertetik az egyes legfelső szintű ingatlanokat. Eltérő rendelkezés hiányában minden választható.

## <a name="aggregator"></a>Aggregátor

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Az Azure [Cosmos DB eseményindító és kötések konfigurációs](functions-bindings-cosmosdb.md)beállításai.

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
|GatewayMode (Átjáró mód)|Átjáró|A függvény által az Azure Cosmos DB szolgáltatáshoz való csatlakozáskor használt csatlakozási mód. A `Direct` lehetőségek a következők: és`Gateway`|
|Protocol (Protokoll)|Https (https)|A függvény által használt kapcsolati protokoll, amikor az Azure Cosmos DB szolgáltatáshoz való csatlakozáskor.  Olvassa el [itt a magyarázata mindkét mód](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/a|Az alkalmazás összes függvényében használható bérletelőtag.|

## <a name="durabletask"></a>durableTask (tartósfeladat)

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Az [Event Hub eseményindítóinak és kötéseinek konfigurációs](functions-bindings-event-hubs.md)beállításai.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>Funkciók

A feladatgazda által futtatott függvények listája. Az üres tömb az összes függvény futtatását jelenti. Csak [helyi futtatás](functions-run-local.md)esetén használható. Az Azure-beli függvényalkalmazásokban ehelyett kövesse a Funkciók letiltása az Azure Functionsben a [funkciók letiltása](disable-function.md) a beállítás helyett.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Az összes funkció időkimenő időtartamát jelzi. Kiszolgáló nélküli felhasználási tervben az érvényes tartomány 1 másodperctől 10 percig terjed, az alapértelmezett érték pedig 5 perc. Az App Service-csomagban nincs általános korlát, és az alapértelmezett _null ,_ ami azt jelzi, nincs időtúllépés.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

A [Gazdaállapot-figyelő](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)konfigurációs beállításai .

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
|engedélyezve|igaz|Itt adható meg, hogy a szolgáltatás engedélyezve van-e. | 
|állapotellenőrző intervallum|10 másodperc|Az időszakos háttérállapot-ellenőrzések közötti időintervallum. | 
|healthCheckWindow|2 perc|A `healthCheckThreshold` beállítással együtt használt csúszó időablak.| 
|healthCheckThreshold|6|Az állapot-ellenőrzés sikertelensítésének maximális száma a gazdagép újrahasznosításának megkezdése előtt.| 
|counterThreshold|0,80|Az a küszöbérték, amelynél a teljesítményszámláló nem megfelelő állapotúnak minősül.| 

## <a name="http"></a>http

A [http-eseményindítók és -kötések konfigurációs](functions-bindings-http-webhook.md)beállításai.

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

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|dynamicThrottlesEnabled|hamis|Ha engedélyezve van, ez a beállítás hatására a kérelemfeldolgozó folyamat rendszeresen ellenőrzi a rendszer teljesítményszámlálóit, például a kapcsolatokat/szálakat/folyamatokat/memóriát/cpu/etc.- t, és ha ezek közül a számlálók bármelyike magasabb, mint egy beépített magas küszöbérték (80%), a kérelmeket 429 "Túl elfoglalt" válaszsal utasítja el, amíg a számláló(k) vissza nem térnek a normál szintre.|
|maxConcurrentRequests|határtalan (`-1`)|A párhuzamosan végrehajtandó HTTP-függvények maximális száma. Ez lehetővé teszi az egyidejűség szabályozását, amely segíthet az erőforrás-kihasználtság kezelésében. Előfordulhat például, hogy van egy HTTP-függvény, amely sok rendszererőforrást (memória/cpu/socket) használ, így problémákat okoz, ha az egyidejűség túl magas. Vagy előfordulhat, hogy rendelkezik egy funkcióval, amely kimenő kérelmeket küld egy harmadik fél szolgáltatásnak, és ezeket a hívásokat korlátozott mértékben kell végrehajtani. Ezekben az esetekben a fojtószelep alkalmazása itt segíthet.|
|maxOutstandingRequests|határtalan (`-1`)|Az adott időpontban lefolytatott kérelmek maximális száma. Ez a korlát tartalmazza a várólistára helyezett, de még nem elkezdett végrehajtást, valamint a folyamatban lévő végrehajtások. Az ezen a korláton felülérkező bejövő kérelmeket a rendszer 429 "Túl elfoglalt" választ küld. Ez lehetővé teszi a hívók számára, hogy időalapú újrapróbálkozási stratégiákat alkalmazzanak, és segít a maximális kérelem-késések szabályozásához. Ez csak a parancsfájlgazda végrehajtási útvonalán belül előforduló sorban állásokat szabályozza. Más várólisták, például a ASP.NET kérési várólista továbbra is érvényben maradnak, és ez a beállítás nem érinti őket.|
|routePrefix|api-t|Az összes útvonalra vonatkozó útvonalelőtag. Az alapértelmezett előtag eltávolításához használjon üres karakterláncot. |

## <a name="id"></a>id

A feladatgazda egyedi azonosítója. Lehet kisbetűs GUID a kötőjelek eltávolítása. Helyi futtatáskor szükséges. Az Azure-ban való futtatáskor azt javasoljuk, hogy ne állítson be azonosítóértéket. Egy azonosító automatikusan jön létre `id` az Azure-ban, ha nincs megadva. 

Ha több függvényalkalmazáson keresztül oszt meg egy storage-fiókot, győződjön meg arról, hogy minden függvényalkalmazás más. `id` Kihagyhatja a `id` tulajdonságot, vagy manuálisan `id` beállíthatja az egyes függvényalkalmazásokat egy másik értékre. Az időzítő eseményindító egy tárolózárat használ annak biztosítására, hogy csak egy időzítőpéldány legyen, amikor egy függvényalkalmazás több példányra skálázódik. Ha két függvényalkalmazás `id` azonos, és mindegyik időzítő-eseményindítót használ, csak egy időzítő fog futni.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Tuskózó

Szabályozza az [ILogger-objektum](functions-monitoring.md#write-logs-in-c-functions) vagy a [context.log](functions-monitoring.md#write-logs-in-javascript-functions)által írt naplók szűrését.

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
|categoryFilter|n/a|Kategória szerinti szűrést ad meg| 
|defaultLevel|Információ|A `categoryLevels` tömbben nem megadott kategóriák esetén küldjön naplókat ezen a szinten és felette az Application Insightsnak.| 
|categorySzintek|n/a|Kategóriák tömbje, amely meghatározza az egyes kategóriákhoz az Application Insightsnak küldő minimális naplószintet. Az itt megadott kategória az összes olyan kategóriát szabályozza, amely ugyanazzal az értékkel kezdődik, és a hosszabb értékek élveznek elsőbbséget. Az előző *gazdagép.json* mintafájlban az összes olyan kategória, amely `Information` a "Host.Aggregator" naplóval kezdődik. Az összes többi kategória, amely "Host" kezdetű, például `Error` a "Host.Executor", jelentkezzen be a szinten.| 

## <a name="queues"></a>Várólisták

A [tárolási várólista-eseményindítók és -kötések konfigurációs](functions-bindings-storage-queue.md)beállításai.

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
|maxPollingInterval érték|60000|A várólista-szavazások közötti maximális időköz ezredmásodpercben.| 
|láthatóságTimeout|0|Az újrapróbálkozások közötti időintervallum, amikor az üzenet feldolgozása sikertelen.| 
|batchSize|16|A Functions futásidejű várólistán lévő üzenetek száma, amelyeket a Függvények futásidejű lehív és párhuzamosan dolgoz fel. Amikor a feldolgozott szám lesz `newBatchThreshold`a, a futásidejű kap egy másik köteget, és megkezdi az üzenetek feldolgozását. Így a függvényenként feldolgozott egyidejű üzenetek maximális `batchSize` `newBatchThreshold`száma plusz . Ez a korlát külön-külön vonatkozik az egyes várólista által aktivált függvények. <br><br>Ha el szeretné kerülni az egy várólistán fogadott `batchSize` üzenetek párhuzamos végrehajtását, állítsa be az 1-et. Ez a beállítás azonban csak addig szünteti meg az egyidejűséget, amíg a függvényalkalmazás egyetlen virtuális gépen (VM) fut. Ha a függvényalkalmazás több virtuális gépre skálázódik, minden virtuális gép minden egyes várólista által aktivált függvény egy példányát futtathatja.<br><br>A `batchSize` maximális érték 32. | 
|maxDequeueCount (maxDequeueCount)|5|Az üzenetek feldolgozása a méregvárólistába való áthelyezés e sorokba való áthelyezése előtt többször is.| 
|újBatchThreshold|batchSize/2|Amikor a feldolgozás alatt álló üzenetek száma egyidejűleg lesz le erre a számra, a futásidejű lekéri egy másik köteget.| 

## <a name="sendgrid"></a>SendGrid

A [SendGrind kimenetkötés](functions-bindings-sendgrid.md) konfigurációs beállítása

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|honnan|n/a|A feladó e-mail címe az összes funkcióban.| 

## <a name="servicebus"></a>serviceBus

A [Service Bus eseményindítóinak és kötéseinek konfigurációs](functions-bindings-service-bus.md)beállítása.

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
|maxConcurrentKéri|16|Az üzenetszivattyú által kezdeményezett visszahívásegyidejű hívásainak maximális száma. Alapértelmezés szerint a Függvények futásidejű folyamatok több üzenetet dolgoznak fel egyidejűleg. Ha azt szeretné, hogy a futásidejű egyszerre csak egy `maxConcurrentCalls` várólista- vagy témakörüzenet feldolgozására szolgáljon, állítsa 1-re. | 
|prefetchCount (előre fetchCount)|n/a|Az alapul szolgáló MessageReceiver által használt alapértelmezett PrefetchCount.| 
|automatikus Megújítottidő-ki|00:05:00|Az a maximális időtartam, amelyen belül az üzenetzárolás automatikusan megújul.| 

## <a name="singleton"></a>Singleton

A Singleton zárolási viselkedés konfigurációs beállításai. További információ: [GitHub-probléma a singleton támogatással kapcsolatban.](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)

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
|lockPeriod (zárolásidőszak)|00:00:15|Az az időszak, amelyalatt a függvényszintű zárolások elkészültek. A zárak automatikusan megújulnak.| 
|listenerLockPeriod|00:01:00|Az az időszak, amely et a figyelő zárak venni.| 
|listenerLockRecoveryPollingInterval|00:01:00|A figyelő zárolási helyreállításhoz használt időintervallum, ha a figyelőzárolás indításkor nem szerezhető be.| 
|lockAcquisitionTimeout|00:01:00|A futásidejű maximális időtartam, amíg a futásidejű megpróbálja megszerezni a zárolást.| 
|lockAcquisitionPollingInterval|n/a|A zárolási kísérletek közötti időköz.| 

## <a name="tracing"></a>Nyomkövetés

*1.x verzió*

Az `TraceWriter` objektum használatával létrehozott naplók konfigurációs beállításai. Lásd: [C# Naplózás](functions-reference-csharp.md#logging) és [Node.js naplózás](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consoleLevel|Info|A konzolnaplózás nyomkövetési szintje. A lehetőségek `off` `error`a `warning` `info`következők: `verbose`, , , , és .|
|fileLoggingMode|debugOnly|A fájlnaplózás nyomkövetési szintje. A `never`lehetőségek `always` `debugOnly`a , , .| 

## <a name="watchdirectories"></a>watchDirectories

[Megosztott kódkönyvtárak](functions-reference-csharp.md#watched-directories) készlete, amelyeket figyelni kell a változásokmiatt.  Biztosítja, hogy amikor a könyvtárakban lévő kódot módosítják, a módosításokat a függvények is felvegyék.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a host.json fájl frissítéséhez](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Globális beállítások megtekintése a környezeti változókban](functions-app-settings.md)
