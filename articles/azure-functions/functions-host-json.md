---
title: Host. JSON-hivatkozás Azure Functions 2. x rendszerhez
description: A v2 futtatókörnyezettel rendelkező Azure Functions Host. JSON fájl dokumentációja.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: cc982d3f810c944a5273cbf0cf9778076d119692
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208824"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Host. JSON-hivatkozás Azure Functions 2. x és újabb verziókhoz 

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2. verzió +](functions-host-json.md)

A *Host. JSON* metaadat-fájl olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function app összes funkcióját érintik. Ez a cikk azokat a beállításokat sorolja fel, amelyek a Azure Functions futtatókörnyezet 2. x verziójával kezdődnek.  

> [!NOTE]
> Ez a cikk Azure Functions 2. x és újabb verziókban használható.  Az 1. x függvények Host. JSON fájljának hivatkozását lásd: [Host. JSON-dokumentáció Azure functions 1. x-hez](functions-host-json-v1.md).

Az [alkalmazás beállításaiban](functions-app-settings.md)az egyéb Function app konfigurációs beállításai is kezelhetők.

Néhány gazdagép. JSON-beállítás csak akkor használatos, ha helyileg fut a [Local. Settings. JSON](functions-run-local.md#local-settings-file) fájlban.

## <a name="sample-hostjson-file"></a>Példa Host. JSON fájlra

A 2. x + verzióban a következő minta *Host. JSON* fájl minden lehetséges beállítást tartalmaz (kivéve azokat, amelyek csak belső használatra vonatkoznak).

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
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 1.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 0.1,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
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

A Application Insights vezérlőelem-beállításai, beleértve a [mintavételi beállításokat](./functions-monitoring.md#configure-sampling)is.

A teljes JSON-struktúra esetében tekintse meg a korábbi [példában a Host. JSON fájlt](#sample-hostjson-file).

> [!NOTE]
> A napló mintavételezése miatt előfordulhat, hogy egyes végrehajtások nem jelennek meg a Application Insights figyelő paneljén. A naplók mintavételezésének elkerüléséhez adja hozzá `samplingExcludedTypes: "Request"` a `applicationInsights` értékhez.

| Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| samplingSettings | n/a | Lásd: [applicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| samplingExcludedTypes | null | Pontosvesszővel tagolt lista, amelyet nem szeretne mintát venni. A felismert típusok: függőség, esemény, kivétel, oldalmegtekintés, kérelem, nyomkövetés. A rendszer továbbítja a megadott típusok összes példányát. a nem megadott típusok mintául szolgálnak. |
| samplingIncludedTypes | null | A mintavételhez használni kívánt típusok pontosvesszővel tagolt listája; az üres lista minden típust magában foglalja. Írja be az itt felsorolt `samplingExcludedTypes` felülbírálási típusok listán szereplő típust. A felismert típusok: függőség, esemény, kivétel, oldalmegtekintés, kérelem, nyomkövetés. A rendszer továbbítja a megadott típusok összes példányát. a nem megadott típusok mintául szolgálnak. |
| enableLiveMetrics | true | Élő metrikák gyűjtésének engedélyezése. |
| enableDependencyTracking | true | A függőségek követésének engedélyezése. |
| enablePerformanceCountersCollection | true | Engedélyezi a kudu teljesítményszámlálók gyűjtését. |
| liveMetricsInitializationDelay | 00:00:15 | Kizárólag belső használatra |
| httpAutoCollectionOptions | n/a | Lásd: [applicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | n/a | Lásd: [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| isEnabled | true | Engedélyezheti vagy letilthatja a mintavételezést. | 
| maxTelemetryItemsPerSecond | 20 | Az egyes kiszolgálók gazdagépén másodpercenként naplózott telemetria-elemek megcélzott száma. Ha az alkalmazás számos gazdagépen fut, csökkentse ezt az értéket, hogy az a forgalom teljes célján belül maradjon. | 
| evaluationInterval | 01:00:00 | A telemetria aktuális sebességének újraértékelésének időköze. A kiértékelést mozgóátlagként kell végrehajtani. Előfordulhat, hogy le kívánja rövidíteni ezt az intervallumot, ha a telemetria a hirtelen törtek. |
| initialSamplingPercentage| 1.0 | A mintavételi folyamat elején alkalmazott kezdeti mintavételezési arány dinamikusan változik a százalékos arányban. Hibakeresés közben ne csökkentse az értéket. |
| samplingPercentageIncreaseTimeout | 00:00:01 | A mintavételezés százalékos értékének megváltozásakor ez a tulajdonság határozza meg, hogy a későbbiekben milyen hamar Application Insights a mintavételezési százalékos arány a további adatmennyiségek rögzítéséhez. |
| samplingPercentageDecreaseTimeout | 00:00:01 | A mintavételezés százalékos értékének megváltozásakor ez a tulajdonság határozza meg, hogy a későbbiekben milyen hamar Application Insights a mintavételezési százalékos arány a kevesebb adattal való rögzítéshez. |
| minSamplingPercentage | 0,1 | Mivel a mintavételezési százalék változó, ez a tulajdonság határozza meg a minimálisan megengedett mintavételi százalékot. |
| maxSamplingPercentage | 0,1 | Mivel a mintavételezési százalék változó, ez a tulajdonság határozza meg a maximálisan megengedett mintavételi százalékot. |
| movingAverageRatio | 1.0 | A mozgóátlag kiszámításakor a legutóbbi értékhez rendelt súlyozást. 1 értékkel egyenlő vagy annál kisebb értéket használjon. A kisebb értékek miatt az algoritmus kevésbé lesz újraaktiválva a hirtelen változásokkal. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. httpAutoCollectionOptions

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Engedélyezheti vagy letilthatja a http-eseményindítók kiterjesztett HTTP-kérelmi információit: bejövő kérelmek korrelációs fejlécei, többtényezős kulcsok támogatása, HTTP-metódus, elérési út és válasz. |
| enableW3CDistributedTracing | true | Engedélyezheti vagy letilthatja a W3C elosztott nyomkövetési protokoll támogatását (és bekapcsolja az örökölt korrelációs sémát). Alapértelmezés szerint engedélyezve van, ha a `enableHttpTriggerExtendedInfoCollection` igaz. Ha `enableHttpTriggerExtendedInfoCollection` hamis, akkor ez a jelző csak a kimenő kérelmekre vonatkozik, nem a bejövő kérelmekre. |
| enableResponseHeaderInjection | true | Engedélyezheti vagy letilthatja a többszörös összetevők korrelációs fejlécének a válaszokban való befecskendezését. Az injekció engedélyezése lehetővé teszi, hogy Application Insights egy alkalmazás-hozzárendelést, hogy több rendszerállapot-kulcs legyen használatban. Alapértelmezés szerint engedélyezve van, ha a `enableHttpTriggerExtendedInfoCollection` igaz. Ez a beállítás nem érvényes, ha a `enableHttpTriggerExtendedInfoCollection` hamis. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

A pillanatképekkel kapcsolatos további információkért tekintse meg a [.NET-alkalmazásokban előforduló kivételek hibakeresését](/azure/azure-monitor/app/snapshot-debugger) ismertető témakört, valamint a [Application Insights Snapshot Debugger vagy Pillanatképek megtekintését engedélyező problémákat](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| agentEndpoint | null | A Application Insights Snapshot Debugger szolgáltatáshoz való kapcsolódáshoz használt végpont. Ha NULL, a rendszer egy alapértelmezett végpontot használ. |
| captureSnapshotMemoryWeight | 0,5 | A folyamat aktuális memória-méretéhez megadott súlyozás, ha a rendszer ellenőrzi, hogy van-e elég memória a pillanatkép elvégzéséhez. A várt érték nagyobb, mint 0 megfelelő tört (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | A telemetria-processzor letiltása előtt a pillanatképek kérésére vonatkozó sikertelen kérelmek számának korlátozása.|
| handleUntrackedExceptions | true | Engedélyezheti vagy letilthatja a Application Insights telemetria által nem követett kivételek nyomon követését. |
| isEnabled | true | Pillanatkép-gyűjtemények engedélyezése vagy letiltása | 
| isEnabledInDeveloperMode | false | A pillanatkép-gyűjtemények engedélyezése vagy letiltása fejlesztői módban. |
| isEnabledWhenProfiling | true | Engedélyezheti vagy letilthatja a pillanatképek létrehozását, még akkor is, ha a Application Insights Profiler részletes profilkészítési munkamenetet gyűjt. |
| isExceptionSnappointsEnabled | false | Engedélyezheti vagy letilthatja a kivételek szűrését. |
| isLowPrioritySnapshotUploader | true | Meghatározza, hogy a SnapshotUploader folyamat a normál prioritás alatt fusson-e. |
| maximumCollectionPlanSize | 50 | Az egyiktől a 9999-ig terjedő tartományban bármikor nyomon követhető problémák maximális száma. |
| maximumSnapshotsRequired | 3 | Egy adott problémára összegyűjtött Pillanatképek maximális száma az egyiktől a 999-ig terjedő tartományban. Probléma lehet az alkalmazás egyéni Throw utasítása. Ha a probléma miatt összegyűjtött Pillanatképek száma eléri ezt az értéket, a rendszer nem gyűjt további pillanatképeket a problémához, amíg vissza nem állítja a problémát számlálókat (lásd: `problemCounterResetInterval`), és a `thresholdForSnapshotting` korlátja ismét elérhető lesz. |
| problemCounterResetInterval | 24:00:00 | Milyen gyakran kell alaphelyzetbe állítani a probléma számlálóit egy perc és hét nap között. Ha eléri ezt az időközt, a rendszer az összes probléma számát nullára állítja vissza. Azok a meglévő problémák, amelyek már elértek a pillanatképek elvégzéséhez szükséges küszöbértéket, de még nem generálták a `maximumSnapshotsRequired`ban lévő Pillanatképek számát, aktívak maradnak. |
| provideAnonymousTelemetry | true | Meghatározza, hogy a névtelen használatot és a hibát telemetria kell-e küldeni a Microsoftnak. Ez a telemetria akkor használható, ha felveszi a kapcsolatot a Microsofttal a Snapshot Debugger problémáinak elhárítása érdekében. A használati minták figyelésére is használható. |
| reconnectInterval | 00:15:00 | Az Snapshot Debugger-végponthoz való Újrakapcsolódás gyakorisága. A megengedett tartomány egy perc és egy nap között lehet. |
| shadowCopyFolder | null | A bináris fájlok árnyékmásolat-másolásához használandó mappát adja meg. Ha nincs beállítva, az alábbi környezeti változók által megadott mappák a következő sorrendben lesznek kipróbálva: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Ha az értéke igaz, a SnapshotUploader csak egy példánya gyűjt és tölt fel pillanatképeket több, a InstrumentationKey-t használó alkalmazáshoz. Ha hamis értékre van állítva, a SnapshotUploader minden (ProcessName, InstrumentationKey) rekord esetében egyedi lesz. |
| snapshotInLowPriorityThread | true | Meghatározza, hogy a pillanatképek feldolgozása alacsony IO-prioritású szálon történjen-e. A pillanatképek létrehozása gyors művelet, de a pillanatképnek a Snapshot Debugger szolgáltatásba való feltöltéséhez először a lemezre kell írni minidump. Ez a SnapshotUploader folyamat során történik. Ha ezt az értéket igaz értékre állítja, az alacsony prioritású IO-t használja a minidump írásához, amely nem fog versenyezni az alkalmazás erőforrásaival. Ha ezt az értéket hamis értékre állítja, a minidump létrehozása az alkalmazás lelassulásának rovására kerül. |
| snapshotsPerDayLimit | 30 | Egy nap alatt engedélyezett Pillanatképek maximális száma (24 óra). Ez a korlát a Application Insights szolgáltatási oldalon is érvényben van. A feltöltések díjszabása napi 50-ra korlátozódik (ez a rendszerállapot-kulcs). Ez az érték segít megelőzni a feltöltés során a végül elutasított további pillanatképek létrehozását. A nulla érték teljesen eltávolítja a korlátot, ami nem ajánlott. |
| snapshotsPerTenMinutesLimit | 1 | A pillanatképek maximális száma 10 percen belül. Bár ehhez az értékhez nincs felső korlát, körültekintően kell megnövelni az éles számítási feladatokhoz, mert ez hatással lehet az alkalmazás teljesítményére. A pillanatképek létrehozása gyorsan elvégezhető, de a pillanatkép minidump létrehozása és a Snapshot Debugger szolgáltatásba való feltöltése sokkal lassabb művelet, amely az erőforrásokhoz (CPU és I/O) való alkalmazással verseng. |
| tempFolder | null | Meghatározza a minidumps és a feltöltő naplófájljainak írására szolgáló mappát. Ha nincs beállítva, akkor a rendszer a *%temp%\Dumps* használja. |
| thresholdForSnapshotting | 1 | Hányszor Application Insights kell megtekintenie egy kivételt, mielőtt megkéri a pillanatképeket. |
| uploaderProxy | null | Felülbírálja a pillanatkép-feltöltő folyamat során használt proxykiszolgálót. Előfordulhat, hogy ezt a beállítást kell használnia, ha az alkalmazás egy proxykiszolgálón keresztül csatlakozik az internethez. A Snapshot Collector az alkalmazás folyamatán belül fut, és ugyanazokat a proxybeállításokat fogja használni. A pillanatkép-feltöltő azonban külön folyamatként fut, és előfordulhat, hogy manuálisan kell konfigurálnia a proxykiszolgálót. Ha ez az érték null, a Snapshot Collector megkísérli automatikusan felderíteni a proxy címeit a System .net. Request. DefaultWebProxy és a pillanatkép-feltöltő értékének átadásával. Ha ez az érték nem null, akkor a rendszer nem használja az automatikus észlelést, és az itt megadott proxykiszolgálót fogja használni a pillanatkép-Feltöltőben. |

## <a name="cosmosdb"></a>cosmosDb

A konfigurációs beállítás [Cosmos db eseményindítókban és kötésekben](functions-bindings-cosmosdb-v2.md#host-json)található.

## <a name="durabletask"></a>durableTask

A konfigurációs beállítás a [Durable functions kötéseiben](durable/durable-functions-bindings.md#host-json)található.

## <a name="eventhub"></a>eventHub

A konfigurációs beállítások az [Event hub-eseményindítókban és-kötésekben](functions-bindings-event-hubs.md#host-json)találhatók. 

## <a name="extensions"></a>Extensions

Tulajdonság, amely egy olyan objektumot ad vissza, amely tartalmazza az összes kötési beállítást, például a [http](#http) -t és a [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

A bővítmények lehetővé teszik, hogy a Function alkalmazáshoz hozzáadjon egy kompatibilis functions-bővítményt. További információ: [bővítmények a helyi fejlesztéshez](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>funkciók

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

A konfigurációs beállítások a http- [Eseményindítók és-kötések](functions-bindings-http-webhook-output.md#hostjson-settings)szolgáltatásban találhatók.

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
|logLevel|n/a|Az alkalmazásban lévő függvények naplózási kategóriájának szűrését meghatározó objektum. A 2. x és újabb verziók esetében kövesse a naplózási kategória szűrésének ASP.NET Core elrendezését. Ezzel a beállítással szűrheti az adott függvények naplózását. További információ: [naplózási szűrés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) a ASP.net Core dokumentációjában. |
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

Ez az érték a Host. JSON séma-verzióját jelzi. A v2-futtatókörnyezetet vagy újabb verziót célzó Function alkalmazáshoz szükséges `"version": "2.0"`. Nincs gazdagép. JSON séma a v2 és v3 közötti változásokhoz.

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
