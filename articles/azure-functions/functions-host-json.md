---
title: host.json hivatkozás az Azure Functions 2.x-hez
description: Az Azure Functions host.json fájl referenciadokumentációja a v2 futásidejű.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3d98be2dcc351aa88b9e126c883865079e407c2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473370"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>host.json hivatkozás az Azure Functions 2.x és újabb függvényekhez 

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Functions futásidejű verzióját: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2+ verzió](functions-host-json.md)

A *host.json* metaadatfájl olyan globális konfigurációs beállításokat tartalmaz, amelyek hatással vannak egy függvényalkalmazás összes függvényére. Ez a cikk az Azure Functions futásidejű 2.x-es verziójától kezdve elérhető beállításokat sorolja fel.  

> [!NOTE]
> Ez a cikk az Azure Functions 2.x és újabb verziók.  A host.json 1.x függvényében a [host.json témakörben az Azure Functions 1.x host.json hivatkozása](functions-host-json-v1.md)látható.

A függvényalkalmazás egyéb konfigurációs beállításait az [alkalmazásbeállítások](functions-app-settings.md) (telepített alkalmazások esetében) vagy a [local.settings.json](functions-run-local.md#local-settings-file) fájl (helyi fejlesztéshez) kezelik.

A host.json kötésekkel kapcsolatos konfigurációi a függvényalkalmazás minden függvényére egyformán vonatkoznak. 

## <a name="sample-hostjson-file"></a>Minta állomás.json fájl

A következő minta *host.json* fájl 2.x+ verzióhoz az összes lehetséges beállításmeg van adva (kivéve azokat, amelyek csak belső használatra szolgálnak).

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
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
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

A cikk következő szakaszai ismertetik az egyes legfelső szintű ingatlanokat. Eltérő rendelkezés hiányában minden választható.

## <a name="aggregator"></a>Aggregátor

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Ez a beállítás a [naplózás](#logging)gyermeke.

Szabályozza az Application Insights beállításait, beleértve a [mintavételi beállításokat](./functions-monitoring.md#configure-sampling)is.

A teljes JSON-struktúrát lásd a korábbi [példa host.json fájlban.](#sample-hostjson-file)

> [!NOTE]
> Naplómintavételi előfordulhat, hogy egyes végrehajtások nem jelennek meg az Application Insights figyelő panelen. A naplómintavétel elkerülése `samplingExcludedTypes: "Request"` érdekében adja hozzá az `applicationInsights` értéket.

| Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| samplingSettings (mintavételi beállítások) | n/a | Lásd [applicationInsights.samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | igaz | Engedélyezi az élő metrikák gyűjtését. |
| enableDependencyTracking | igaz | Lehetővé teszi a függőségek nyomon követését. |
| enablePerformanceCountersCollection | igaz | Engedélyezi a Kudu teljesítményszámlálók gyűjteményét. |
| liveMetricsInitializationDelay | 00:00:15 | Csak belső használatra. |
| httpAutoCollectionOptions | n/a | Lásd [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | n/a | Lásd [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| engedélyezve van | igaz | Engedélyezi vagy letiltja a mintavételezést. | 
| maxTelemetryItemsPerSecond | 20 | Az egyes kiszolgálógazdagépen másodpercenként naplózott telemetriai elemek célszáma. Ha az alkalmazás sok gazdaszámítógépen fut, csökkentse ezt az értéket, hogy a forgalom általános célarányán belül maradjon. | 
| evaluationInterval | 01:00:00 | Az az időköz, amelyben a telemetriai adatok aktuális sebességét újraértékeli. A kiértékelést mozgóátlagként végezzük. Érdemes lehet lerövidíteni ezt az időközt, ha a telemetriai adatok hajlamosak a hirtelen adatlöket. |
| initialSamplingSzázalék| 1.0 | A mintavételi folyamat kezdetén alkalmazott kezdeti mintavételi százalék dinamikusan változik a százalékos arányban. Ne csökkentse az értéket hibakeresés közben. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Amikor a mintavételi százalékos érték megváltozik, ez a tulajdonság határozza meg, hogy milyen hamar application insights engedélyezett mintavételi százalék ot újra több adat rögzítésére. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Amikor a mintavételi százalék értéke megváltozik, ez a tulajdonság határozza meg, hogy milyen hamar application insights lehetővé teszi, hogy csökkentse a mintavételi százalékot újra kevesebb adatot. |
| minSamplingSzázalék | 0,1 | A mintavételi százalék változó, ez a tulajdonság határozza meg a minimálisan engedélyezett mintavételi százalék. |
| maxSamplingSzázalék | 0,1 | A mintavételi százalék változó, ez a tulajdonság határozza meg a maximálisan megengedett mintavételi százalék. |
| mozgóÁtlagarány | 1.0 | A mozgóátlag kiszámításakor a legutóbbi értékhez rendelt súly. Használjon 1-es vagy annál kisebb értéket. A kisebb értékek miatt az algoritmus kevésbé reagál a hirtelen változásokra. |
| kizárttípusok | null | Pontosvesszővel tagolt lista azokról a típusokról, amelyekről nem szeretne mintát venni. A felismert típusok a következők: Függőség, Esemény, Kivétel, PageView, Kérés, Nyomkövetés. A megadott típusok összes példánya továbbításra kerül; a nem megadott típusok mintavételezése. |
| includedTypes | null | A mintavételezett típusok pontosvesszővel tagolt listája; egy üres lista minden típust magában foglalja. Az itt `excludedTypes` felsorolt felülbírálási típusokban felsorolt típus. A felismert típusok a következők: Függőség, Esemény, Kivétel, PageView, Kérés, Nyomkövetés. A megadott típusok összes példánya továbbításra kerül; a nem megadott típusok mintavételezése. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| engedélyezésHttpTriggerExtendedInfoCollection | igaz | Engedélyezi vagy letiltja a HTTP-eseményindítók kiterjesztett HTTP-kérelemadatait: bejövő kérelemkorrelációs fejlécek, többinstrumentációs kulcsok támogatása, HTTP-módszer, elérési út és válasz. |
| enableW3CDistributedTracing | igaz | Engedélyezi vagy letiltja a W3C elosztott nyomkövetési protokoll támogatását (és bekapcsolja az örökölt korrelációs sémát). Alapértelmezés szerint `enableHttpTriggerExtendedInfoCollection` engedélyezve van, ha igaz. Ha `enableHttpTriggerExtendedInfoCollection` hamis, ez a jelző csak a kimenő kérelmekre vonatkozik, a bejövő kérelmekre nem. |
| enableResponseHeaderInjection | igaz | Engedélyezi vagy letiltja a többkomponensű korrelációs fejlécek befecskendezését a válaszokba. Az injektálás engedélyezése lehetővé teszi, hogy az Application Insights alkalmazásleképezést hozhat létre, ha több instrumentation kulcsok használata esetén. Alapértelmezés szerint `enableHttpTriggerExtendedInfoCollection` engedélyezve van, ha igaz. Ez a beállítás nem `enableHttpTriggerExtendedInfoCollection` érvényes, ha hamis. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguration

A pillanatképekről további információt a [.NET-alkalmazások kivételeiről készített pillanatképek hibakeresése](/azure/azure-monitor/app/snapshot-debugger) és [az Application Insights Snapshot Debugger engedélyezésével vagy a pillanatképek megtekintésével kapcsolatos hibaelhárítás című témakörben talál.](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| ügynökVégpont | null | Az Application Insights Snapshot Debugger szolgáltatáshoz való csatlakozáshoz használt végpont. Ha null, a rendszer alapértelmezett végpontot használ. |
| captureSnapshotMemoryWeight | 0,5 | Az aktuális folyamatmemória méretének súlya annak ellenőrzésekor, hogy elegendő memória áll-e fenn a pillanatkép készítéséhez. A várt érték nagyobb, mint 0 megfelelő tört (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | A telemetriai processzor letiltása előtt pillanatképek kérésére irányuló sikertelen kérelmek számának korlátja.|
| handleUntrackedKivételek | igaz | Engedélyezi vagy letiltja az Application Insights telemetria által nem nyomon követett kivételek nyomon követését. |
| engedélyezve van | igaz | Pillanatkép-gyűjtemény engedélyezése vagy letiltása | 
| isEnabledInDeveloperMode mód | hamis | Engedélyezi vagy letiltja a pillanatkép-gyűjteményt fejlesztői módban. |
| isEnabledWhenProfiling | igaz | Engedélyezi vagy letiltja a pillanatkép létrehozását, még akkor is, ha az Application Insights Profiler részletes profilkészítési munkamenetet gyűjt. |
| isExceptionSnappointsEnabled | hamis | Engedélyezi vagy letiltja a kivételek szűrését. |
| isLowPrioritySnapshotUploader | igaz | Azt határozza meg, hogy a SnapshotUploader folyamat a normál prioritás alatt fusson-e. |
| maximumCollectionPlanSize | 50 | A problémák maximális száma, amelyeket bármikor nyomon tudunk követni egy től 9999-ig terjedő tartományban. |
| maximumSnapshotsRequired | 3 | Egyetlen probléma esetén gyűjtött pillanatképek maximális száma egy és 999 közötti tartományban. A probléma lehet gondolni, mint egy egyéni dobja nyilatkozatot a kérelmet. Amint a probléma miatt összegyűjtött pillanatképek száma eléri ezt az értéket, a probléma további pillanatképei `problemCounterResetInterval`nem `thresholdForSnapshotting` lesznek összegyűjtve a probléma visszaállításáig (lásd) és a korlát újra elérése. |
| problemCounterResetInterval | 24:00:00 | A problémaszámlálók alaphelyzetbe állítása egy perctől hét napig terjedő tartományban. Ha ezt az intervallumot eléri, az összes problémaszám nullára áll vissza. Meglévő problémák, amelyek már elérték a pillanatképek készítésének küszöbértékét, de `maximumSnapshotsRequired`még nem hozták létre a pillanatképek számát a rendszerben, aktívak maradnak. |
| provideAnonymousTelemetry | igaz | Meghatározza, hogy küldjön-e névtelen használati és hibatelemetriai adatokat a Microsoftnak. Ez a telemetriai adatok akkor használhatók, ha a Pillanatkép-hibakeresővel kapcsolatos problémák elhárításához a Microsofthoz lép kapcsolatba. Azt is használják a használati minták figyelésére. |
| újrakapcsolódásIdő | 00:15:00 | Milyen gyakran csatlakozik újra a Snapshot Debugger végponthoz. A megengedett tartomány egy perc és egy nap. |
| árnyékCopyFolder | null | Megadja az árnyékmásolat-másoláshoz használandó mappát. Ha nincs beállítva, a következő környezeti változók által megadott mappákat a rendszer a következő sorrendben próbálkozik: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | igaz | Ha igaz, a SnapshotUploader nek csak egy példánya gyűjti össze és tölti fel a pillanatképeket több olyan alkalmazáshoz, amelyek megosztják a InstrumentationKey-t. Ha hamis, a SnapshotUploader lesz egyedi minden (ProcessName, InstrumentationKey) rekordok. |
| snapshotInLowPriorityThread | igaz | Azt határozza meg, hogy a pillanatképek feldolgozása alacsony I/o-prioritású szálban történik-e. A pillanatkép létrehozása gyors művelet, de a pillanatkép pillanatképnek a Snapshot Debugger szolgáltatásba való feltöltéséhez először minidump ként kell írni a lemezre. Ez történik a SnapshotUploader folyamat. Ha ezt az értéket true értékre állítja, alacsony prioritású I/O-t használ a minidump írásához, amely nem versenyez az erőforrás-alkalmazással. Ha ezt az értéket hamis sebességre állítja a minidump létrehozásához az alkalmazás lelassításának rovására. |
| pillanatképekPerDayLimit | 30 | Az egy nap alatt engedélyezett pillanatképek maximális száma (24 óra). Ez a korlát is érvényesíti az Application Insights szolgáltatás oldalán. A feltöltések aránya alkalmazásonként (azaz instrumentációs kulcsonként) naponta legfeljebb 50 főre korlátozódik. Ez az érték segít megakadályozni további pillanatképek létrehozását, amelyeket végül a feltöltés során elutasítanak. A nulla érték teljesen eltávolítja a korlátot, ami nem ajánlott. |
| pillanatképPerTenMinutesLimit | 1 | A 10 perc alatt engedélyezett pillanatképek maximális száma. Bár nincs felső határa ezt az értéket, legyen óvatosan növelve az éles számítási feladatok, mert hatással lehet az alkalmazás teljesítményére. A pillanatkép létrehozása gyors, de a pillanatkép minidump létrehozása és a Snapshot Debugger szolgáltatásba való feltöltése sokkal lassabb művelet, amely versenyezni fog az alkalmazással az erőforrásokért (mind a CPU, mind az I/O). |
| tempFolder mappa | null | Megadja a minidumps és a feltöltő naplófájljainak írásához szükséges mappát. Ha nincs beállítva, akkor *a %TEMP%\Dumps* lesz használva. |
| thresholdForSnapshotting | 1 | Hányszor Application Insights kell látnia egy kivételt, mielőtt pillanatképeket kér. |
| uploaderProxy | null | Felülbírálja a Pillanatkép feltöltője folyamatban használt proxykiszolgálót. Előfordulhat, hogy ezt a beállítást kell használnia, ha az alkalmazás proxykiszolgálón keresztül csatlakozik az internethez. A Snapshot Collector fut az alkalmazás folyamatán belül, és ugyanazokat a proxybeállításokat fogja használni. A Snapshot Uploader azonban külön folyamatként fut, és előfordulhat, hogy manuálisan kell konfigurálnia a proxykiszolgálót. Ha ez az érték null értékű, akkor a Snapshot Collector a System.Net.WebRequest.DefaultWebProxy vizsgálatával és az érték pillanatkép-feltöltőnek való átadásával automatikusan megpróbálja észlelni a proxy címét. Ha ez az érték nem null, akkor az automatikus észlelés nem lesz használva, és az itt megadott proxykiszolgáló t használja a Pillanatkép-feltöltőben. |

## <a name="cosmosdb"></a>cosmosDb között

A konfigurációs beállítás a [Cosmos DB eseményindítókban és kötésekben](functions-bindings-cosmosdb-v2-output.md#host-json)található.

## <a name="durabletask"></a>durableTask (tartósfeladat)

A konfigurációs beállítás a [Tartós függvények kötéseiben](durable/durable-functions-bindings.md#host-json)található.

## <a name="eventhub"></a>eventHub

A konfigurációs beállítások az [Event Hub eseményindítóiban és kötéseiben](functions-bindings-event-hubs-output.md#host-json)találhatók. 

## <a name="extensions"></a>Kiterjesztés

Olyan tulajdonság, amely az összes kötésspecifikus beállítást tartalmazó objektumot adja vissza, például a [http](#http) és [az eventHub.](#eventhub)

## <a name="extensionbundle"></a>extensionBundle 

A bővítménycsomagok lehetővé teszik a Functions kötési bővítmények kompatibilis készletének hozzáadását a függvényalkalmazáshoz. További információ: [Extension bundles for local development](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>Funkciók

A feladatgazda által futtatott függvények listája. Az üres tömb az összes függvény futtatását jelenti. Csak [helyi futtatás](functions-run-local.md)esetén használható. Az Azure-beli függvényalkalmazásokban ehelyett kövesse a Funkciók letiltása az Azure Functionsben a [funkciók letiltása](disable-function.md) a beállítás helyett.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Az összes funkció időkimenő időtartamát jelzi. A timespan karakterlánc formátumot követi. Kiszolgáló nélküli felhasználási tervben az érvényes tartomány 1 másodperctől 10 percig terjed, az alapértelmezett érték pedig 5 perc.  

A prémium csomagban az érvényes tartomány 1 másodperc és 60 perc között van, az alapértelmezett érték pedig 30 perc.

Egy dedikált (App Service) csomag, nincs általános korlát, és az alapértelmezett érték 30 perc. A nem `-1` kötött végrehajtást jelzi, de ajánlott egy rögzített felső határt tartani.

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

A konfigurációs beállítások a [http-eseményindítókban és kötésekben](functions-bindings-http-webhook-output.md#hostjson-settings)találhatók.

## <a name="logging"></a>naplózás

A függvényalkalmazás naplózási viselkedését szabályozza, beleértve az Application Insightsot is.

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
|fileLoggingMode|debugOnly|Azt határozza meg, hogy milyen szintű fájlnaplózás engedélyezve van.  A `never`lehetőségek `always` `debugOnly`a , , . |
|Loglevel|n/a|Objektum, amely meghatározza a naplókategória szűrését az alkalmazás függvényeihez. A 2.x-es és újabb verziók a ASP.NET Core elrendezést követik a naplókategória-szűréshez. Ez a beállítás lehetővé teszi a naplózás szűrését bizonyos funkciók hoz. További információ: [Naplószűrés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) a ASP.NET Core dokumentációban. |
|konzol|n/a| A [konzol](#console) naplózási beállítása. |
|applicationInsights|n/a| Az [applicationInsights](#applicationinsights) beállítás. |

## <a name="console"></a>konzol

Ez a beállítás a [naplózás](#logging)gyermeke. Ez szabályozza a konzol naplózását, ha nem hibakeresési módban.

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
|engedélyezve van|hamis|Engedélyezi vagy letiltja a konzolnaplózást.| 

## <a name="manageddependency"></a>managedDependency (függőség)

A felügyelt függőség olyan szolgáltatás, amely jelenleg csak a PowerShell-alapú függvények által támogatott. Lehetővé teszi, hogy a függőségeket a szolgáltatás automatikusan feligazgatja. Ha `enabled` a tulajdonság `true`beállítása, `requirements.psd1` a fájl feldolgozása megtörténik. A függőségek az alverziók megjelenésekor frissülnek. További információ: [Felügyelt függőség](functions-reference-powershell.md#dependency-management) a PowerShell-cikkben.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>Várólisták

A konfigurációs beállítások a [Tároló várólista-eseményindítókban és -kötésekben](functions-bindings-storage-queue-output.md#host-json)találhatók.  

## <a name="sendgrid"></a>sendGrid

A konfigurációs beállítás megtalálható a [SendGrid eseményindítókban és kötésekben.](functions-bindings-sendgrid.md#host-json)

## <a name="servicebus"></a>serviceBus

A konfigurációs beállítás megtalálható a [Service Bus eseményindítókban és kötésekben.](functions-bindings-service-bus-output.md#host-json)

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

## <a name="version"></a>version

Ez az érték a host.json sémaverzióját jelzi. A verziókarakterlánc `"version": "2.0"` szükséges egy függvényalkalmazáshoz, amely a v2 futásidejűt vagy egy későbbi verziót célozza meg. A v2 és a v3 között nincsenek host.json sémamódosítások.

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
