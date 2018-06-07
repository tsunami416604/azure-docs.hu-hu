---
title: Az Azure Application Insights adatmodell |} Microsoft Docs
description: A folyamatos exportálás a JSON-ból exportált, és használhatja tulajdonságait ismerteti.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: mbullwin
ms.openlocfilehash: 841b11421b7198efc7fc9bb3226c7b067fcbbdc4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598827"
---
# <a name="application-insights-export-data-model"></a>Application Insights exportálási adatmodell
A következő táblázat által küldött telemetriai tulajdonságainak a [Application Insights](app-insights-overview.md) SDK-k a portálra.
Látni fogja, ezeket a tulajdonságokat a kimeneti adatok a [a folyamatos exportálás](app-insights-export-telemetry.md).
Is megjelennek a tulajdonságszűrők [metrika Explorer](app-insights-metrics-explorer.md) és [diagnosztikai keresési](app-insights-diagnostic-search.md).

Vegye figyelembe a következő szempontok:

* `[0]` Ezek a táblázatok azt jelenti, az elérési út beszúrása index; esetében az ponttá de nem mindig 0.
* Idő időtartamok vannak mikroszekundum, így száma 10 000 000 tized == 1 másodperc.
* Dátum és idő (UTC), és azokról az ISO-formátumban `yyyy-MM-DDThh:mm:ss.sssZ`


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
Telemetria minden típusú környezetben szakasz mellett. Ezek a mezők közül nem mindegyik továbbít minden adatok ponttal.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| Context.Custom.Dimensions [0] |[objektum] |Kulcs-érték párokat egyéni tulajdonságok paraméter határozza meg. Maximális kulcshossz 100, értékek 1024 karakternél. 100-nál több egyedi értékeket, a tulajdonság kereshető, de nem használható Szegmentálás. Maximális 200 kulcsok ikey száma. |
| Context.Custom.Metrics [0] |[objektum] |Kulcs-érték párok egyéni mértékek paraméter és TrackMetrics beállítása. Maximális kulcshossz 100, értékek lehetnek numerikus. |
| context.data.eventTime |karakterlánc |UTC |
| context.data.isSynthetic |logikai |Kérelem úgy tűnik, hogy egy botot vagy webes teszt származhat. |
| context.data.samplingRate |szám |A portál küldött SDK által generált telemetriai százalékát. Tartomány 0,0-100.0. |
| Context.Device |objektum |Ügyféleszközök |
| Context.Device.Browser |karakterlánc |IE Chrome... |
| context.device.browserVersion |karakterlánc |Chrome 48.0... |
| context.device.deviceModel |karakterlánc | |
| context.device.deviceName |karakterlánc | |
| Context.Device.ID |karakterlánc | |
| Context.Device.Locale |karakterlánc |en GB-os, de-DE... |
| Context.Device.Network |karakterlánc | |
| context.device.oemName |karakterlánc | |
| context.device.osVersion |karakterlánc |Gazda operációs rendszer |
| context.device.roleInstance |karakterlánc |Kiszolgáló állomás azonosítója |
| context.device.roleName |karakterlánc | |
| Context.Device.Type |karakterlánc |Számítógép, a böngésző... |
| Context.Location |objektum |Ügyfélip származik. |
| Context.location.City |karakterlánc |Ügyfélip, származik, ha ismert |
| Context.location.ClientIP |karakterlánc |Utolsó nyolcszög anonimizált adatokon alapul, 0-ra. |
| Context.location.Continent |karakterlánc | |
| Context.location.Country |karakterlánc | |
| Context.location.province |karakterlánc |Állam vagy megye |
| Context.Operation.ID |karakterlánc |Azonos művelet azonosítóval rendelkező elemek sablonobjektumhoz kapcsolódó elemként megjelennek a portálon. Általában a kérelem azonosítója. |
| Context.Operation.Name |karakterlánc |URL-cím vagy a kérelem |
| context.operation.parentId |karakterlánc |Lehetővé teszi, hogy a beágyazott kapcsolódó elemek. |
| Context.Session.ID |karakterlánc |Műveletek ugyanarról a forrásról csoportjának azonosítója. 30 percig művelet nélkül jelzi a munkamenet végén. |
| context.session.isFirst |logikai | |
| context.user.accountAcquisitionDate |karakterlánc | |
| context.user.anonAcquisitionDate |karakterlánc | |
| context.user.anonId |karakterlánc | |
| context.user.authAcquisitionDate |karakterlánc |[Hitelesített felhasználó](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |logikai | |
| internal.data.documentVersion |karakterlánc | |
| Internal.Data.ID |karakterlánc | Amikor egy elem okozhatnak az Application Insights hozzárendelt egyedi azonosítója |

## <a name="events"></a>Események
Egyéni események által generált [trackevent() függvény](app-insights-api-custom-events-metrics.md#trackevent).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] események száma |egész szám |100 / ([mintavételi](app-insights-sampling.md) sebessége). Például 4 =&gt; 25 %. |
| [0] esemény neve |karakterlánc |Az esemény neve.  Legfeljebb 250. |
| [0] esemény URL-címe |karakterlánc | |
| [0] esemény urlData.base |karakterlánc | |
| [0] esemény urlData.host |karakterlánc | |

## <a name="exceptions"></a>Kivételek
Jelentések [kivételek](app-insights-asp-net-exceptions.md) a kiszolgálón, valamint a böngészőben.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| szerelvény [0] basicException |karakterlánc | |
| [0] basicException száma |egész szám |100 / ([mintavételi](app-insights-sampling.md) sebessége). Például 4 =&gt; 25 %. |
| [0] basicException exceptionGroup |karakterlánc | |
| [0] basicException exceptionType |karakterlánc | |
| [0] basicException failedUserCodeMethod |karakterlánc | |
| [0] basicException failedUserCodeAssembly |karakterlánc | |
| [0] basicException handledAt |karakterlánc | |
| [0] basicException hasFullStack |logikai | |
| [0] basicException azonosítója |karakterlánc | |
| [0] basicException módszer |karakterlánc | |
| [0] basicException üzenet |karakterlánc |Kivétel üzenetét. Legfeljebb 10 KB-os. |
| [0] basicException outerExceptionMessage |karakterlánc | |
| [0] basicException outerExceptionThrownAtAssembly |karakterlánc | |
| [0] basicException outerExceptionThrownAtMethod |karakterlánc | |
| [0] basicException outerExceptionType |karakterlánc | |
| [0] basicException outerId |karakterlánc | |
| basicException [0] [0] parsedStack szerelvény |karakterlánc | |
| basicException [0] [0] parsedStack fájlnév |karakterlánc | |
| basicException [0] [0] parsedStack szint |egész szám | |
| basicException [0] [0] parsedStack sor |egész szám | |
| basicException [0] [0] parsedStack módszer |karakterlánc | |
| [0] basicException verem |karakterlánc |Legfeljebb 10 KB-os |
| [0] basicException typeName |karakterlánc | |

## <a name="trace-messages"></a>Nyomkövetési üzenetek
Által küldött [TrackTrace](app-insights-api-custom-events-metrics.md#tracktrace), és a [naplózási adapterek](app-insights-asp-net-trace-logs.md).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| üzenet [0] naplózó_neve |karakterlánc | |
| [0] Paraméterek |karakterlánc | |
| nyers [0] üzenet |karakterlánc |A naplóüzenet 10 KB-os karakternél. |
| üzenet [0] súlyossági szint |karakterlánc | |

## <a name="remote-dependency"></a>Távoli függőség
TrackDependency által küldött. Jelentés teljesítményét és használatának használt [függőségek hívásainak](app-insights-asp-net-dependencies.md) a kiszolgálón, és az AJAX-hívások a böngészőben.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] remoteDependency aszinkron |logikai | |
| [0] remoteDependency baseName |karakterlánc | |
| [0] remoteDependency commandName |karakterlánc |Például "otthoni/index" |
| [0] remoteDependency száma |egész szám |100 / ([mintavételi](app-insights-sampling.md) sebessége). Például 4 =&gt; 25 %. |
| [0] remoteDependency dependencyTypeName |karakterlánc |HTTP, SQL... |
| [0] remoteDependency durationMetric.value |szám |A hívás befejezését függőség válasz ideje |
| [0] remoteDependency azonosítója |karakterlánc | |
| [0] remoteDependency neve |karakterlánc |URL-címe. Legfeljebb 250. |
| [0] remoteDependency resultCode |karakterlánc |a HTTP-függőség |
| [0] remoteDependency sikeres |logikai | |
| [0] remoteDependency típusa |karakterlánc |HTTP, Sql... |
| [0] remoteDependency URL-címe |karakterlánc |Maximális hossz 2000 |
| [0] remoteDependency urlData.base |karakterlánc |Maximális hossz 2000 |
| [0] remoteDependency urlData.hashTag |karakterlánc | |
| [0] remoteDependency urlData.host |karakterlánc |Maximális hossz 200 |

## <a name="requests"></a>Kérelmek
Által küldött [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest). A globális modulok ezzel jelentések kiszolgáló válaszideje, mérni a kiszolgálón.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] kérelmek száma |egész szám |100 / ([mintavételi](app-insights-sampling.md) sebessége). Például: 4 =&gt; 25 %. |
| kérelem [0] durationMetric.value |szám |A válasz érkező kérelmek időpontját. 1e7 == 1s |
| a kérelemazonosító [0] |karakterlánc |Műveletazonosító |
| [0] kérelem neve |karakterlánc |GET/POST + alap URL-je.  Legfeljebb 250 |
| kérelem [0] responseCode |egész szám |Az ügyfélnek küldött HTTP-válasz |
| [0] kérés sikeres |logikai |Alapértelmezett == (responseCode &lt; 400) |
| [0] kérelem URL-címe |karakterlánc |Nem többek között a gazdagépen |
| kérelem [0] urlData.base |karakterlánc | |
| kérelem [0] urlData.hashTag |karakterlánc | |
| kérelem [0] urlData.host |karakterlánc | |

## <a name="page-view-performance"></a>Teljesítmény nézet
A böngésző által küldött. Egy oldal, a felhasználó a kérés (kivéve az aszinkron AJAX-hívások) teljes megjelenítendő kezdeményezése feldolgozni időt méri.

Ügyfél OS és böngészőverzió környezetben értékek megjelenítése

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] clientPerformance clientProcess.value |egész szám |Megjelenítésére fognak az oldal HTML befogadására záró időpontját. |
| [0] clientPerformance neve |karakterlánc | |
| [0] clientPerformance networkConnection.value |egész szám |A hálózati kapcsolat létrehozásához szükséges idő. |
| [0] clientPerformance receiveRequest.value |egész szám |Küldi a kérelmet a válaszban fogadásával HTML végének időpontját. |
| [0] clientPerformance sendRequest.value |egész szám |Az idő-e a HTTP-kérelem küldése. |
| [0] clientPerformance total.value |egész szám |Elküldeni a kérelmet a lap megjelenítése kezdési időpontot. |
| [0] clientPerformance URL-címe |karakterlánc |A kérelem URL-címe |
| [0] clientPerformance urlData.base |karakterlánc | |
| [0] clientPerformance urlData.hashTag |karakterlánc | |
| [0] clientPerformance urlData.host |karakterlánc | |
| [0] clientPerformance urlData.protocol |karakterlánc | |

## <a name="page-views"></a>Lapmegtekintések
TrackPageView() által küldött vagy [stopTrackPage](app-insights-api-custom-events-metrics.md#page-views)

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] számának megtekintése |egész szám |100 / ([mintavételi](app-insights-sampling.md) sebessége). Például 4 =&gt; 25 %. |
| [0] durationMetric.value megtekintése |egész szám |Értéke nem kötelezően trackPageView() vagy startTrackPage() - stopTrackPage(). Nem ugyanaz, mint clientPerformance értékeket. |
| [0] nézet neve |karakterlánc |Lap címe.  Legfeljebb 250 |
| [0] nézet URL-címe |karakterlánc | |
| [0] urlData.base megtekintése |karakterlánc | |
| [0] urlData.hashTag megtekintése |karakterlánc | |
| [0] urlData.host megtekintése |karakterlánc | |

## <a name="availability"></a>Rendelkezésre állás
Jelentések [webteszt rendelkezésre állási](app-insights-monitor-web-app-availability.md).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| rendelkezésre állási [0] availabilityMetric.name |karakterlánc |rendelkezésre állás |
| rendelkezésre állási [0] availabilityMetric.value |szám |1.0 vagy 0,0-nál |
| rendelkezésre állási [0] száma |egész szám |100 / ([mintavételi](app-insights-sampling.md) sebessége). Például 4 =&gt; 25 %. |
| rendelkezésre állási [0] dataSizeMetric.name |karakterlánc | |
| rendelkezésre állási [0] dataSizeMetric.value |egész szám | |
| rendelkezésre állási [0] durationMetric.name |karakterlánc | |
| rendelkezésre állási [0] durationMetric.value |szám |Vizsgálat időtartama. 1e7 == 1s |
| rendelkezésre állási [0] üzenet |karakterlánc |Diagnosztikai hiba |
| rendelkezésre állási [0] eredménye |karakterlánc |Pass/sikertelen |
| rendelkezésre állási [0] runLocation |karakterlánc |Http-kérés földrajzi forrása |
| rendelkezésre állási [0] testName |karakterlánc | |
| rendelkezésre állási [0] testRunId |karakterlánc | |
| rendelkezésre állási [0] testTimestamp |karakterlánc | |

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

## <a name="about-metric-values"></a>Metrika értékek
Metrika értékek, mind a metrika és a máshol, szabványos objektum struktúrával jelenti. Példa:

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

Jelenleg -, ha ez megváltozhatnak a jövőben - az összes értéket jelentett SDK modulban `count==1` és csak a `name` és `value` mezők lehetnek hasznosak. Az egyetlen eset, ahol azok eltérő lenne a lenne, ha a saját TrackMetric hívás írási amely csoportban a többi paraméter.

A többi mező az a célja, hogy lehetővé tegye a mérni kívánt összesíteni az SDK-t, a portál forgalom csökkentése érdekében. Például több egymást követő értékek sikerült átlagos minden metrika jelentés elküldése előtt. Majd ehhez kiszámítása a min, max, szórás és összesített értékét (sum vagy átlagos) és count beállítva a jelentés által képviselt értékek száma.

A fenti táblázatokban igazolnia kell nincs megadva a ritkán használt mezők számát, min, max, szórás és sampledValue.

Előre összesítése metrikák helyett használhat [mintavételi](app-insights-sampling.md) Ha telemetriai adatok mennyisége csökkenteni kell.

### <a name="durations"></a>Időtartamok
Jelzés hiányában időtartamok vannak megadva a mikroszekundum tized, hogy 10000000.0 azt jelenti, hogy 1 másodperc.

## <a name="see-also"></a>Lásd még
* [Application Insights](app-insights-overview.md)
* [A folyamatos exportálás](app-insights-export-telemetry.md)
* [Kódminták](app-insights-export-telemetry.md#code-samples)
