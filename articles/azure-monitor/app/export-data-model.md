---
title: Az Azure Application Insights-adatmodellt |} A Microsoft Docs
description: A folyamatos exportálás JSON-ból exportált, és használja szűrőként tulajdonságait ismerteti.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: mbullwin
ms.openlocfilehash: c906f313ae367fcc660d1ce1df0b3d667c7f04a3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016117"
---
# <a name="application-insights-export-data-model"></a>Application Insights exportálási Data Model
Ez a táblázat által küldött telemetriát tulajdonságait a [Application Insights](../../application-insights/app-insights-overview.md) SDK-k a portálra.
Láthatja, hogy ezeket a tulajdonságokat az adatkimenet bármilyen [folyamatos exportálás](export-telemetry.md).
Is megjelennek a tulajdonságszűrők [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) és [diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md).

Tudnivalók:

* `[0]` a táblázatokban azt jelzi, hogy egy pont az elérési út, amelyekben egy index beszúrása de nem mindig 0.
* Idő időtartamok vannak mikromásodperces, így száma 10 000 000 tized == 1 másodperc.
* Dátum és idő (UTC), és az ISO formátumban van megadva `yyyy-MM-DDThh:mm:ss.sssZ`


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
Az összes típusú telemetriát környezet szakasz egészül ki. Ezek a mezők közül nem mindegyik küldött az összes adatpont.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| Context.Custom.Dimensions [0] |[objektum] |Kulcs – érték sztringpárok egyéni tulajdonságok paraméter határozza meg. Maximális kulcshossz 100, értékeket 1024 karakternél. A tulajdonság több mint 100 egyedi értékeket, kereshető, de nem használható a Szegmentálás. Maximális száma 200 kulcsok száma rendszerállapotkulcsot. |
| Context.Custom.Metrics [0] |[objektum] |Állítsa be az egyéni mértékek paraméter és a TrackMetrics kulcs-érték párokat. Maximális kulcshossz 100 értékek lehetnek numerikus. |
| context.data.eventTime |sztring |UTC |
| context.data.isSynthetic |logikai |Kérelem úgy tűnik, hogy egy robot vagy a webes tesztből származnak. |
| context.data.samplingRate |szám |A telemetriát a portálra küldött SDK által generált százalékos értéke. Tartomány 0,0-100.0. |
| Context.Device |objektum |Ügyféleszközök |
| Context.Device.Browser |sztring |IE Chrome-ot vagy... |
| context.device.browserVersion |sztring |Chrome 48.0... |
| context.device.deviceModel |sztring | |
| context.device.deviceName |sztring | |
| Context.Device.ID |sztring | |
| Context.Device.Locale |sztring |de-DE, en-GB, az... |
| Context.Device.Network |sztring | |
| context.device.oemName |sztring | |
| context.device.osVersion |sztring |Gazda operációs rendszer |
| context.device.roleInstance |sztring |Kiszolgáló állomás azonosítója |
| context.device.roleName |sztring | |
| Context.Device.Type |sztring |Számítógép esetén a böngészőben... |
| Context.Location |objektum |Ügyfélip származik. |
| Context.location.City |sztring |Ha az ismert ügyfélip, származik |
| Context.location.ClientIP |sztring |Utolsó nyolcszöggel anonimok 0-ra. |
| Context.location.Continent |sztring | |
| Context.location.Country |sztring | |
| Context.location.province |sztring |Állam vagy megye |
| Context.Operation.ID |sztring |A művelet azonos azonosítóval rendelkező elem sablonobjektumhoz kapcsolódó elemként jelenik meg a portálon. Általában a kérelem azonosítója. |
| Context.Operation.Name |sztring |URL-cím vagy kérelem |
| context.operation.parentId |sztring |Lehetővé teszi, hogy a beágyazott kapcsolódó elemek. |
| Context.Session.ID |sztring |Az azonos forrásból érkező műveletek egy csoportját azonosítója. Egy műveletet anélkül 30 percig jeleket a munkamenet végén. |
| context.session.isFirst |logikai | |
| context.user.accountAcquisitionDate |sztring | |
| context.user.anonAcquisitionDate |sztring | |
| context.user.anonId |sztring | |
| context.user.authAcquisitionDate |sztring |[Hitelesített felhasználó](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |logikai | |
| internal.data.documentVersion |sztring | |
| Internal.Data.ID |sztring | Ha egy elem betöltött Application Insights hozzárendelt egyedi azonosítója |

## <a name="events"></a>Események
Egyéni események által generált [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] események száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| [0] esemény neve |sztring |Esemény neve.  Maximális hossz 250. |
| [0] esemény URL-címe |sztring | |
| [0] esemény urlData.base |sztring | |
| [0] esemény urlData.host |sztring | |

## <a name="exceptions"></a>Kivételek
Jelentések [kivételek](../../azure-monitor/app/asp-net-exceptions.md) a kiszolgálón, valamint a böngészőben.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| szerelvény [0] basicException |sztring | |
| [0] basicException száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| [0] basicException exceptionGroup |sztring | |
| [0] basicException exceptionType |sztring | |
| [0] basicException failedUserCodeMethod |sztring | |
| [0] basicException failedUserCodeAssembly |sztring | |
| [0] basicException handledAt |sztring | |
| [0] basicException hasFullStack |logikai | |
| [0] basicException azonosítója |sztring | |
| [0] basicException metódus |sztring | |
| [0] basicException üzenet |sztring |Kivételre vonatkozó üzenet. Maximális hossz: 10k. |
| [0] basicException outerExceptionMessage |sztring | |
| [0] basicException outerExceptionThrownAtAssembly |sztring | |
| [0] basicException outerExceptionThrownAtMethod |sztring | |
| [0] basicException outerExceptionType |sztring | |
| [0] basicException outerId |sztring | |
| parsedStack [0] szerelvény [0] basicException |sztring | |
| [0] basicException parsedStack [0] fájlnév |sztring | |
| basicException [0] [0] parsedStack szint |egész szám | |
| basicException [0] [0] parsedStack sor |egész szám | |
| basicException [0] [0] parsedStack metódus |sztring | |
| [0] basicException verem |sztring |Maximális hossz: 10k |
| a typeName basicException [0] |sztring | |

## <a name="trace-messages"></a>Nyomkövetési üzenetek
Által küldött [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace), illetve a [naplózási adapterek](../../azure-monitor/app/asp-net-trace-logs.md).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] üzenet naplózó_neve |sztring | |
| üzenet [0] Paraméterek |sztring | |
| nyers [0] üzenet |sztring |A naplófájlüzenetre maximális hossz: 10k. |
| [0] üzenet err |sztring | |

## <a name="remote-dependency"></a>Távoli függőségek
TrackDependency által küldött. Jelentés teljesítményét és használatát használt [függőségekhez intézett hívások](../../azure-monitor/app/asp-net-dependencies.md) a kiszolgálón, és az AJAX-hívások a böngészőben.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| az aszinkron remoteDependency [0] |logikai | |
| [0] remoteDependency baseName |sztring | |
| [0] remoteDependency commandName |sztring |Például "home/index" |
| [0] remoteDependency száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| [0] remoteDependency dependencyTypeName |sztring |HTTP, SQL... |
| [0] remoteDependency durationMetric.value |szám |A befejezési válasz a függőségi hívás ideje |
| [0] remoteDependency azonosítója |sztring | |
| [0] remoteDependency neve |sztring |URL-címe. Maximális hossz 250. |
| [0] remoteDependency eredménykód |sztring |a HTTP-függőségek |
| [0] remoteDependency sikeres |logikai | |
| [0] remoteDependency típusa |sztring |HTTP, Sql... |
| [0] remoteDependency URL-címe |sztring |Maximális hossz 2000 |
| [0] remoteDependency urlData.base |sztring |Maximális hossz 2000 |
| [0] remoteDependency urlData.hashTag |sztring | |
| [0] remoteDependency urlData.host |sztring |Maximális hossza 200 |

## <a name="requests"></a>Kérelmek
Által küldött [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). A globális modulok ezzel jelentések kiszolgálói válaszidő mérése a kiszolgálón.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] kérések száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Példa: 4 =&gt; 25 %. |
| kérelem [0] durationMetric.value |szám |A válasz érkező kérelem ideje. 1e7 == 1s |
| a [0] kérés azonosítója |sztring |Műveletazonosító |
| [0] kérelem neve |sztring |GET/POST + alap URL-címét.  Maximális hossz 250 |
| kérelem [0] responseCode |egész szám |Az ügyfélnek küldött HTTP-válasz |
| [0] sikeres kérelem |logikai |Alapértelmezett == (responseCode &lt; 400) |
| [0] kérelem URL-címe |sztring |Nem többek között a host |
| kérelem [0] urlData.base |sztring | |
| kérelem [0] urlData.hashTag |sztring | |
| kérelem [0] urlData.host |sztring | |

## <a name="page-view-performance"></a>Oldal nézet teljesítmény
A böngésző által küldött. Egy oldal, a felhasználótól a kérelem teljes (kivéve az aszinkron AJAX-hívások) megjeleníthető kezdeményezése feldolgozni időt méri.

Környezet értékek megjelenítése az ügyfél operációs rendszer és a böngésző verziója.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] clientPerformance clientProcess.value |egész szám |A HTML-megjelenítés, a lap azért végétől ideje. |
| [0] clientPerformance neve |sztring | |
| [0] clientPerformance networkConnection.value |egész szám |A hálózati kapcsolat ideje. |
| [0] clientPerformance receiveRequest.value |egész szám |Idő a végén a kérést küld a válasz fogadása a HTML-kódot. |
| [0] clientPerformance sendRequest.value |egész szám |A Time-e a HTTP-kérelem küldése. |
| [0] clientPerformance total.value |egész szám |Idő elindítását, a kérést küldhet az oldal megjelenítése. |
| [0] clientPerformance URL-címe |sztring |A kérelem URL-címe |
| [0] clientPerformance urlData.base |sztring | |
| [0] clientPerformance urlData.hashTag |sztring | |
| [0] clientPerformance urlData.host |sztring | |
| [0] clientPerformance urlData.protocol |sztring | |

## <a name="page-views"></a>Oldalmegtekintések
TrackPageView() által küldött vagy [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| [0] durationMetric.value megtekintése |egész szám |Igény szerint hozzáadhat trackPageView() vagy startTrackPage() - érték stopTrackPage(). Nem ugyanaz, mint clientPerformance értékeket. |
| [0] nézet neve |sztring |Lap címe.  Maximális hossz 250 |
| url megtekintése [0] |sztring | |
| [0] urlData.base megtekintése |sztring | |
| [0] urlData.hashTag megtekintése |sztring | |
| [0] urlData.host megtekintése |sztring | |

## <a name="availability"></a>Rendelkezésre állás
Jelentések [rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| rendelkezésre állási [0] availabilityMetric.name |sztring |rendelkezésre állás |
| rendelkezésre állási [0] availabilityMetric.value |szám |1.0-s vagy 0,0 |
| rendelkezésre állási [0] száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| rendelkezésre állási [0] dataSizeMetric.name |sztring | |
| rendelkezésre állási [0] dataSizeMetric.value |egész szám | |
| rendelkezésre állási [0] durationMetric.name |sztring | |
| rendelkezésre állási [0] durationMetric.value |szám |Vizsgálat időtartama. 1e7 == 1s |
| rendelkezésre állási [0] üzenet |sztring |Diagnosztikai hiba |
| [0] rendelkezésre állási eredmény |sztring |Pass/sikertelen |
| rendelkezésre állási [0] runLocation |sztring |Http-kérés földrajzi forrása |
| rendelkezésre állási [0] testName |sztring | |
| rendelkezésre állási [0] testRunId |sztring | |
| rendelkezésre állási [0] testTimestamp |sztring | |

## <a name="metrics"></a>Mérőszámok
A trackmetric() függvény által létrehozott.

A metrika érték megtalálható context.custom.metrics[0]

Példa:

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

## <a name="about-metric-values"></a>Metrikaértékek kapcsolatban
Standard objektum struktúra metrikaértékek metrikai jelentések és a máshol, is jelenti. Példa:

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

Jelenleg – Bár a előfordulhat, hogy módosítja a jövőben – az összes értéket a standard szintű SDK modulok által jelentett `count==1` , és csak a `name` és `value` mezők hasznosak. Az egyetlen eset, ahol lennének különböző lenne, ha a saját TrackMetric-hívások írási amely csoportban a többi paraméter.

A többi mező célja, hogy a metrikák, hogy az SDK-ban, a forgalom csökkentése érdekében a portálra. Például több egymást követő olvasmányok sikerült átlagos egyes metrikai jelentés elküldése előtt. Ezután lenne kiszámítása a min, max, szórás és összesített érték (sum vagy átlag), és állítsa száma a jelentés által képviselt értékek száma.

A fenti táblázatokban a ritkán használt mezők száma, min, max, stdDev és sampledValue kihagytunk azt.

Előre összesítése metrikák helyett használhat [mintavételi](../../azure-monitor/app/sampling.md) Ha lerövidítheti a telemetriai adatok mennyisége.

### <a name="durations"></a>Időtartamok
Azzal a különbséggel, jelezve, időtartamok tized-mikromásodperces jelennek meg, hogy 10000000.0 azt jelenti, hogy 1 másodperc.

## <a name="see-also"></a>Lásd még
* [Application Insights](../../application-insights/app-insights-overview.md)
* [A folyamatos exportálás](export-telemetry.md)
* [Kódminták](export-telemetry.md#code-samples)
