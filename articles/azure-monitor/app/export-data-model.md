---
title: Az Azure Application Insights adatmodellje | Microsoft Docs
description: Leírja a folyamatos exportálásból a JSON-ban exportált tulajdonságokat, és szűrőként használatos.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: e4dd2310169476e54c06083fee11b2e4cccecd8d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663875"
---
# <a name="application-insights-export-data-model"></a>Application Insights adatmodell exportálása
Ez a táblázat a [Application Insights](../../azure-monitor/app/app-insights-overview.md) SDK-k által a portálra eljuttatott telemetria tulajdonságait sorolja fel.
Ezek a tulajdonságok a [folyamatos exportálásból](export-telemetry.md)származó adatkimenetben jelennek meg.
Emellett megjelennek a tulajdonságok szűrői a [metrika-kezelőben](../../azure-monitor/app/metrics-explorer.md) és a [diagnosztikai keresésben](../../azure-monitor/app/diagnostic-search.md).

Tudnivalók:

* az alábbi táblázatokban szereplő `[0]` egy olyan pontot jelöl, amelyben az indexet be kell szúrnia. Ez azonban nem mindig 0.
* Az időtartamok egy másodpercen belül vannak, így 10000000 = = 1 másodperc.
* A dátumok és időpontok UTC, és ISO formátumban vannak megadva `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Példa
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Környezet
A telemetria összes típusát egy környezeti szakasz kíséri. A mezők nem mindegyike kerül továbbításra minden adatponttal.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| Context. Custom. dimensions [0] |objektum [] |Egyéni tulajdonságok paraméterrel beállított kulcs-érték karakterlánc-párok. A kulcs maximális hossza 100, az értékek maximális hossza 1024. Több mint 100 egyedi érték, a tulajdonság kereshető, de nem használható szegmentáláshoz. Maximális 200 kulcs/rendszerállapotkulcsot. |
| context.custom.metrics [0] |objektum [] |Egyéni mérési paraméterek és TrackMetrics szerint beállított kulcs-érték párok. A kulcs maximális hossza 100, az értékek numerikusak lehetnek. |
| Context. eventTime |sztring |UTC |
| context.data.isSynthetic |logikai |Úgy tűnik, hogy a kérelem egy robotból vagy egy webes tesztből származik. |
| context.data.samplingRate |szám |A telemetriát a portálra küldött SDK által generált százalékos értéke. Tartomány 0,0-100.0. |
| környezet. eszköz |objektum |Ügyféleszközök |
| Context. Device. Browser |sztring |IE, Chrome,... |
| Context. Device. browserVersion |sztring |Chrome 48,0,... |
| Context. Device. deviceModel |sztring | |
| Context. Device. deviceName |sztring | |
| context.device.id |sztring | |
| környezet. eszköz. területi beállítás |sztring |en-GB, de-DE,... |
| kontextus. Device. Network |sztring | |
| Context. Device. oemName |sztring | |
| Context. Device. os |sztring | |
| Context. Device. osVersion |sztring |Gazdagép operációs rendszere |
| context.device.roleInstance |sztring |Kiszolgáló állomás azonosítója |
| context.device.roleName |sztring | |
| Context. Device. screenResolution |sztring | |
| Context. Device. Type |sztring |SZÁMÍTÓGÉP, böngésző,... |
| környezet. hely |objektum |Ügyfélip származtatva. |
| kontextus. location. City |sztring |Ügyfélip származtatva, ha ismert |
| Context. location. ügyfélip |sztring |Az utolsó nyolcszög értéke 0. |
| Context. location. kontinens |sztring | |
| környezet. hely. ország |sztring | |
| környezet. hely. tartomány |sztring |Állam vagy megye |
| context.operation.id |sztring |Az azonos műveleti azonosítóval rendelkező elemek a portálon kapcsolódó elemként jelennek meg. Általában a kérelem azonosítója. |
| context.operation.name |sztring |URL-cím vagy kérelem neve |
| context.operation.parentId |sztring |Lehetővé teszi a beágyazott kapcsolódó elemek használatát. |
| context.session.id |sztring |Ugyanabból a forrásból származó műveletek csoportjának azonosítója. Egy művelet nélküli, 30 perces időszak a munkamenet végét jelzi. |
| Context. Session. isFirst |logikai | |
| Context. user. accountAcquisitionDate |sztring | |
| context.user.accountId |sztring | |
| context.user.anonAcquisitionDate |sztring | |
| context.user.anonId |sztring | |
| context.user.authAcquisitionDate |sztring |[Hitelesített felhasználó](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |sztring | |
| context.user.isAuthenticated |logikai | |
| Context. user. storeRegion |sztring | |
| internal.data.documentVersion |sztring | |
| internal.data.id |sztring | Az elemek betöltésének Application Insightséhez hozzárendelt egyedi azonosító |

## <a name="events"></a>Események
A [TrackEvent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)által generált egyéni események.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| esemény [0] darabszám |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Például: 4 =&gt; 25%. |
| esemény [0] név |sztring |Esemény neve.  Maximális hosszúság 250. |
| esemény [0] URL-cím |sztring | |
| esemény [0] urlData. Base |sztring | |
| esemény [0] urlData. Host |sztring | |

## <a name="exceptions"></a>Kivételek
A kiszolgáló és a böngésző [kivételeit](../../azure-monitor/app/asp-net-exceptions.md) jelenti.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| basicException [0] szerelvény |sztring | |
| basicException [0] darabszám |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Például: 4 =&gt; 25%. |
| basicException [0] exceptionGroup |sztring | |
| basicException [0] exceptionType |sztring | |
| basicException [0] failedUserCodeMethod |sztring | |
| basicException [0] failedUserCodeAssembly |sztring | |
| basicException [0] handledAt |sztring | |
| basicException [0] hasFullStack |logikai | |
| basicException [0] azonosító |sztring | |
| basicException [0] metódus |sztring | |
| basicException [0] üzenet |sztring |Kivételi üzenet. Maximális hossz: 10k. |
| basicException [0] outerExceptionMessage |sztring | |
| basicException [0] outerExceptionThrownAtAssembly |sztring | |
| basicException [0] outerExceptionThrownAtMethod |sztring | |
| basicException [0] outerExceptionType |sztring | |
| basicException [0] outerId |sztring | |
| basicException [0] parsedStack [0] szerelvény |sztring | |
| basicException [0] parsedStack [0] fájlnév |sztring | |
| basicException [0] parsedStack [0] szint |egész szám | |
| basicException [0] parsedStack [0] sor |egész szám | |
| basicException [0] parsedStack [0] metódus |sztring | |
| basicException [0] verem |sztring |Maximális hossz 10k |
| basicException [0] typeName |sztring | |

## <a name="trace-messages"></a>Nyomkövetési üzenetek
A [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)és a [naplózási adapterek](../../azure-monitor/app/asp-net-trace-logs.md)küldik.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| üzenet [0] loggerName |sztring | |
| üzenet [0] paraméter |sztring | |
| üzenet [0] nyers |sztring |A log üzenet, a maximális hossz 10k. |
| üzenet [0] severityLevel |sztring | |

## <a name="remote-dependency"></a>Távoli függőség
A TrackDependency küldte. A kiszolgáló [függőségeinek](../../azure-monitor/app/asp-net-dependencies.md) és a BÖNGÉSZŐBEN található Ajax-hívásoknak a teljesítményére és használatára irányuló hívások jelentésére szolgál.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| remoteDependency [0] aszinkron |logikai | |
| remoteDependency [0] baseName |sztring | |
| remoteDependency [0] commandName |sztring |Például "Home/index" |
| remoteDependency [0] darabszám |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Például: 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |sztring |HTTP, SQL,... |
| remoteDependency [0] durationMetric. Value |szám |A meghívástól a válasz befejezésére való függőség szerinti idő |
| remoteDependency [0] azonosító |sztring | |
| remoteDependency [0] név |sztring |URL. Maximális hosszúság 250. |
| remoteDependency [0] resultCode |sztring |HTTP-függőségből |
| remoteDependency [0] sikeres |logikai | |
| remoteDependency [0] típus |sztring |Http, SQL,... |
| remoteDependency [0] URL-cím |sztring |Maximális hossz 2000 |
| remoteDependency [0] urlData. Base |sztring |Maximális hossz 2000 |
| remoteDependency [0] urlData. hashTag |sztring | |
| remoteDependency [0] urlData. Host |sztring |Maximális hossz 200 |

## <a name="requests"></a>Kérelmek
A [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)küldte. A standard modulok ezt a kiszolgálót használják a kiszolgálón mért válaszidő megjelentéséhez.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| kérelem [0] darabszám |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Például: 4 =&gt; 25%. |
| kérelem [0] durationMetric. Value |szám |A kérelemtől érkező válasz időpontja. 1e7 = = 1s |
| kérelem [0] azonosító |sztring |Művelet azonosítója |
| kérelem [0] név |sztring |GET/POST + URL-cím alapja.  Maximális hossz 250 |
| kérelem [0] responseCode |egész szám |Az ügyfélnek küldött HTTP-válasz |
| kérelem [0] sikeres |logikai |Default = = (responseCode &lt; 400) |
| kérelem [0] URL-cím |sztring |Nem tartalmazza a gazdagépet |
| kérelem [0] urlData. Base |sztring | |
| kérelem [0] urlData. hashTag |sztring | |
| kérelem [0] urlData. Host |sztring | |

## <a name="page-view-performance"></a>Az oldal nézetének teljesítménye
A böngésző küldte. Megméri egy oldal feldolgozásának idejét, a felhasználótól a kérelem megjelenítésének befejezését kezdeményező (aszinkron AJAX-hívások kivételével).

A környezeti értékek az ügyfél operációs rendszerének és a böngészőnek a verzióját mutatják.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |egész szám |A HTML-fájl fogadásának vége a lap megjelenítéséhez. |
| clientPerformance [0] név |sztring | |
| clientPerformance [0] networkConnection. Value |egész szám |Hálózati kapcsolat létesítéséhez szükséges idő. |
| clientPerformance [0] receiveRequest. Value |egész szám |A kérelem küldésének időpontja a HTML-válaszban való fogadáshoz. |
| clientPerformance [0] SendRequest hívás. Value |egész szám |A HTTP-kérelem elküldéséhez szükséges idő. |
| clientPerformance [0] Total. Value |egész szám |A kérés elküldésének időpontja a lap megjelenítéséhez. |
| clientPerformance [0] URL-cím |sztring |A kérelem URL-címe |
| clientPerformance [0] urlData. Base |sztring | |
| clientPerformance [0] urlData. hashTag |sztring | |
| clientPerformance [0] urlData. Host |sztring | |
| clientPerformance [0] urlData. Protocol |sztring | |

## <a name="page-views"></a>Lapok nézetei
TrackPageView () vagy [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views) küldte

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| nézet [0] száma |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Például: 4 =&gt; 25%. |
| [0] durationMetric. Value megtekintése |egész szám |Az érték opcionálisan be van állítva a trackPageView () vagy a startTrackPage ()-stopTrackPage () paraméterrel. Nem ugyanaz, mint a clientPerformance értéke. |
| nézet [0] neve |sztring |Lap címe  Maximális hossz 250 |
| [0] URL-cím megtekintése |sztring | |
| [0] urlData. Base megtekintése |sztring | |
| a [0] urlData. hashTag megtekintése |sztring | |
| a (z) [0] urlData. Host megtekintése |sztring | |

## <a name="availability"></a>Rendelkezésre állás
Jelentések [rendelkezésre állását ismertető webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| rendelkezésre állás [0] availabilityMetric.name |sztring |rendelkezésre állás |
| rendelkezésre állás [0] availabilityMetric. Value |szám |1.0-s vagy 0,0 |
| rendelkezésre állás [0] darabszám |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Például: 4 =&gt; 25%. |
| rendelkezésre állás [0] dataSizeMetric.name |sztring | |
| rendelkezésre állás [0] dataSizeMetric. Value |egész szám | |
| rendelkezésre állás [0] durationMetric.name |sztring | |
| rendelkezésre állás [0] durationMetric. Value |szám |A teszt időtartama. 1e7==1s |
| rendelkezésre állás [0] üzenet |sztring |Sikertelen diagnosztika |
| rendelkezésre állás [0] eredmény |sztring |Pass/Fail |
| rendelkezésre állás [0] runLocation |sztring |Http REQ földrajzi forrása |
| rendelkezésre állás [0] testName |sztring | |
| rendelkezésre állás [0] testRunId |sztring | |
| rendelkezésre állás [0] testTimestamp |sztring | |

## <a name="metrics"></a>Mérőszámok
Létrehozta: TrackMetric ().

A metrika értéke a kontextusban található. egyéni. mérőszámok [0]

Például:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>A metrika értékei
A metrikai jelentésekben és máshol is szereplő metrikus értékeket a standard objektum struktúrája jelenti. Például:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Jelenleg – bár ez a jövőben is változhat – a standard SDK-modulok által jelentett összes értéknél `count==1`, és csak a `name` és a `value` mezők hasznosak. Az egyetlen eset, amikor más lenne, ha olyan TrackMetric-hívásokat ír, amelyekben a többi paramétert beállította.

A többi mező célja, hogy lehetővé tegye a metrikák összesítését az SDK-ban, hogy csökkentse a forgalmat a portálon. Előfordulhat például, hogy az egyes metrikai jelentések elküldése előtt több egymást követő beolvasás is megtörténhet. Ezután kiszámítja a minimum, a maximum, a szórás és az összesített érték (összeg vagy átlag) értékét, és a jelentés által képviselt olvasások számát adja meg.

A fenti táblázatokban kihagyta a ritkán használt mezők darabszámát, a min, a Max, a stdDev és a sampledValue.

A metrikák előzetes összesítése helyett [mintavételt](../../azure-monitor/app/sampling.md) is használhat, ha csökkentenie kell a telemetria mennyiségét.

### <a name="durations"></a>Időtartamok
Ha másként nincs jelezve, az időtartamok egy másodperc tizedében jelennek meg, így a 10000000,0 1 másodpercet jelent.

## <a name="see-also"></a>Lásd még
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Folyamatos exportálás](export-telemetry.md)
* [Kódminták](export-telemetry.md#code-samples)
