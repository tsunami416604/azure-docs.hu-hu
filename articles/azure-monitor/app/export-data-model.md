---
title: Azure Application Insights adatmodell | Microsoft dokumentumok
description: A JSON-ban történő folyamatos exportálásból exportált és szűrőként használt tulajdonságok leírása.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: e4dd2310169476e54c06083fee11b2e4cccecd8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663875"
---
# <a name="application-insights-export-data-model"></a>Application Insights exportadat-modell
Ez a táblázat az [Application Insights](../../azure-monitor/app/app-insights-overview.md) SDK-kból a portálra küldött telemetriai adatok tulajdonságait sorolja fel.
Ezeket a tulajdonságokat a [Folyamatos exportálás](export-telemetry.md)adatkimenetében fogja látni.
A [Metrikus kezelő](../../azure-monitor/app/metrics-explorer.md) és a [diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md)tulajdonságszűrőiben is megjelennek.

Megjegyzésre mutat:

* `[0]`ezekben a táblázatokban a görbe egy olyan pontját jelöli, ahol indexet kell beszúrni; de ez nem mindig 0.
* Az időtartamok tized mikroszekundumban vannak, így 10000000 == 1 másodperc.
* A dátumok és időpontok UTC-ek, és ISO formátumban vannak megadva`yyyy-MM-DDThh:mm:ss.sssZ`


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
Minden típusú telemetriai adatok at egy környezeti szakasz kíséri. Nem minden mező kerül továbbításra minden adatponttal.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| context.custom.dimensions [0] |objektum [ ] |Az egyéni tulajdonságok paramétere által beállított kulcs-érték karakterláncpárok. Kulcs maximális hossza 100, értékek maximális hossza 1024. Több mint 100 egyedi érték, a tulajdonság kereshető, de nem használható szegmentálásra. Legfeljebb 200 billentyű/ikey. |
| context.custom.metrics [0] |objektum [ ] |Az egyéni mérési paraméter és a TrackMetrics által beállított kulcsérték-párok. A kulcs maximális hossza 100, az értékek számszerűek lehetnek. |
| context.data.eventTime |sztring |UTC |
| context.data.isSzintetikus |logikai |Kérés úgy tűnik, hogy jön egy bot vagy webes teszt. |
| context.data.samplingRate |szám |A portálra küldött SDK által létrehozott telemetriai adatok százalékos aránya. Tartomány 0.0-100.0. |
| context.device |objektum |Ügyféleszköz |
| context.device.böngésző |sztring |Azaz, Chrome, ... |
| context.device.browserVersion |sztring |Chrome 48.0, ... |
| context.device.deviceModell |sztring | |
| context.device.deviceName |sztring | |
| context.device.id |sztring | |
| context.device.locale |sztring |en-GB, de-DE, ... |
| context.device.network |sztring | |
| context.device.oemName |sztring | |
| context.device.os |sztring | |
| context.device.osVersion |sztring |Gazda operációs rendszer |
| context.device.roleInstance |sztring |Kiszolgálóállomás azonosítója |
| context.device.roleName |sztring | |
| context.device.screenResolution |sztring | |
| context.device.type |sztring |PC, böngésző, ... |
| context.location |objektum |Ügyfélip-ből származik. |
| context.location.city |sztring |Ügyfélip-ből származik, ha ismert |
| context.location.clientip |sztring |Az utolsó nyolcszög et anonimizálták 0-ra. |
| context.location.kontinens |sztring | |
| context.location.country |sztring | |
| context.location.province |sztring |Állam vagy tartomány |
| context.operation.id |sztring |Az azonos műveletazonosítóval rendelkező elemek kapcsolódó elemekként jelennek meg a portálon. Általában a kérelem azonosítója. |
| context.operation.name |sztring |url vagy kérelem neve |
| context.operation.parentId |sztring |Lehetővé teszi a beágyazott kapcsolódó elemeket. |
| context.session.id |sztring |Azonos forrásból származó műveletek csoportjának azonosítója. A művelet nélküli 30 perces időszak jelzi a munkamenet végét. |
| context.session.isFirst |logikai | |
| context.user.accountAcquisitionDate |sztring | |
| context.user.accountId |sztring | |
| context.user.anonAcquisitionDate |sztring | |
| context.user.anonId |sztring | |
| context.user.authAcquisitionDate |sztring |[Hitelesített felhasználó](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |sztring | |
| context.user.isHitelesített |logikai | |
| context.user.storeRégió |sztring | |
| belső.data.documentVersion |sztring | |
| internal.data.id |sztring | Egyedi azonosító, amely akkor van hozzárendelve, ha egy elem be van(adták) az Application Insightsba |

## <a name="events"></a>Események
A [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)által létrehozott egyéni események.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| esemény [0] száma |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) arány). Például 4&gt; = 25%. |
| esemény [0] név |sztring |Esemény neve.  Max hossz 250. |
| esemény [0] url |sztring | |
| esemény [0] urlData.base |sztring | |
| esemény [0] urlData.host |sztring | |

## <a name="exceptions"></a>Kivételek
[Kivételeket](../../azure-monitor/app/asp-net-exceptions.md) jelent a kiszolgálón és a böngészőben.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| basicException [0] összeállítás |sztring | |
| basicException [0] számláló |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) arány). Például 4&gt; = 25%. |
| basicException [0] exceptionGroup |sztring | |
| basicException [0] exceptionType |sztring | |
| basicException [0] failedUserCodeMethod |sztring | |
| basicException [0] failedUserCodeAssembly |sztring | |
| basicException [0] kezelve: |sztring | |
| basicException [0] hasFullStack |logikai | |
| basicException [0] azonosító |sztring | |
| basicException [0] metódus |sztring | |
| basicException [0] üzenet |sztring |Kivétel üzenet. Max hossza 10k. |
| basicException [0] outerExceptionMessage |sztring | |
| basicException [0] outerExceptionThrownAtAssembly |sztring | |
| basicException [0] outerExceptionThrownAtMethod |sztring | |
| basicException [0] outerExceptionType |sztring | |
| basicException [0] külsőazonosító |sztring | |
| basicException [0] elemzőverezés [0] szerelvény |sztring | |
| basicException [0] parsedStack [0] fájlnév |sztring | |
| basicException [0] elemzőverem [0] szint |egész szám | |
| basicException [0] elemzőverem [0] sor |egész szám | |
| basicException [0] elemzőverezés [0] metódus |sztring | |
| basicException [0] verem |sztring |Maximális hossz 10k |
| basicException [0] typeName |sztring | |

## <a name="trace-messages"></a>Nyomkövetési üzenetek
A [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)és a [naplózási adapterek](../../azure-monitor/app/asp-net-trace-logs.md)által küldött .

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| üzenet:[0] loggerName |sztring | |
| üzenet [0] paraméterei |sztring | |
| üzenet [0] nyers |sztring |A napló üzenet, max hossza 10k. |
| üzenet [0] severityLevel |sztring | |

## <a name="remote-dependency"></a>Távoli függőség
A TrackDependency küldte. A kiszolgálón lévő [függőségek és](../../azure-monitor/app/asp-net-dependencies.md) ajax-hívások teljesítményének és használatának jelentésére szolgál a kiszolgálón.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| remoteDependency [0] async |logikai | |
| remoteDependency [0] alapnév |sztring | |
| remoteDependency [0] parancsNév |sztring |Például "home/index" |
| remoteDependency [0] számláló |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) arány). Például 4&gt; = 25%. |
| remoteDependency [0] dependencyTypeName |sztring |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |szám |A hívástól a függőség szerint a válasz befejezéséig töltött idő |
| remoteDependency [0] id |sztring | |
| remoteDependency [0] név |sztring |Url. Max hossz 250. |
| remoteDependency [0] resultCode |sztring |HTTP-függőségből |
| remoteDependency [0] sikeres |logikai | |
| remoteDependency [0] típus |sztring |Http, Sql,... |
| remoteDependency [0] url |sztring |Maximális hossz 2000 |
| remoteDependency [0] urlData.base |sztring |Maximális hossz 2000 |
| remoteDependency [0] urlData.hashTag |sztring | |
| remoteDependency [0] urlData.host |sztring |Maximális hossz 200 |

## <a name="requests"></a>Kérelmek
Által küldött [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). A szabványos modulok ezt használják a kiszolgáló natérben mért válaszidejének jelentésére.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| kérés [0] száma |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) arány). Például: 4&gt; = 25%. |
| kérelem [0] durationMetric.value |szám |A kéréstől a válaszig eltöltött idő. 1e7 == 1 s |
| kérelem [0] id |sztring |Műveleti azonosító |
| kérelem [0] név |sztring |GET/POST + url base.  Max hossz 250 |
| [0] responseCode kérés |egész szám |Ügyfélnek küldött HTTP-válasz |
| kérelem [0] sikeres |logikai |Alapértelmezett == (responseCode &lt; 400) |
| kérelem [0] url |sztring |A gazdagép nem beleértve |
| kérés [0] urlData.base |sztring | |
| kérés [0] urlData.hashTag |sztring | |
| kérés [0] urlData.host |sztring | |

## <a name="page-view-performance"></a>Lapnézet teljesítménye
A böngésző küldte. Méri az oldal feldolgozásának idejét, a felhasználótól kezdve, aki a teljes megjelenítési kérést kezdeményezi (kivéve az aszinkron AJAX-hívásokat).

A környezeti értékek az ügyfél operációs rendszerének és a böngésző verziójának megjelenítése.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |egész szám |A HTML-kód átvételétől az oldal megjelenítéséig eltöltött idő. |
| clientPerformance [0] név |sztring | |
| clientPerformance [0] networkConnection.value |egész szám |A hálózati kapcsolat létesítéséhez szükséges idő. |
| clientPerformance [0] receiveRequest.value |egész szám |A kérés elküldésének végétől a HTML-válasz fogadásáig eltöltött idő. |
| clientPerformance [0] sendRequest.value |egész szám |A HTTP-kérelem elküldéséhez szükséges idő. |
| clientPerformance [0] total.value |egész szám |A kérés elküldésének megkezdésétől az oldal megjelenítéséig. |
| clientPerformance [0] url |sztring |A kérés URL-címe |
| clientPerformance [0] urlData.base |sztring | |
| clientPerformance [0] urlData.hashTag |sztring | |
| clientPerformance [0] urlData.host |sztring | |
| clientPerformance [0] urlData.protocol |sztring | |

## <a name="page-views"></a>Lapmegtekintések
TrackPageView() vagy [stopTrackPage által](../../azure-monitor/app/api-custom-events-metrics.md#page-views) küldött

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| nézet [0] száma |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) arány). Például 4&gt; = 25%. |
| [0] durationMetric.value megtekintése |egész szám |A trackPageView() vagy a startTrackPage() - stopTrackPage() beállítással tetszés szerint beállítható érték. Nem ugyanaz, mint a clientPerformance értékek. |
| nézet [0] neve |sztring |Oldal címe.  Max hossz 250 |
| [0] url megtekintése |sztring | |
| (a) [0] urlData.base |sztring | |
| (a) [0] urlData.hashTag megtekintése |sztring | |
| (a) [0] urlData.host megtekintése |sztring | |

## <a name="availability"></a>Rendelkezésre állás
A [rendelkezésre állási webes tesztek ről](../../azure-monitor/app/monitor-web-app-availability.md)számol be.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| elérhetőség [0] availabilityMetric.name |sztring |availability |
| elérhetőség [0] availabilityMetric.value |szám |1.0 vagy 0.0 |
| elérhetőség [0] |egész szám |100/([mintavételi](../../azure-monitor/app/sampling.md) arány). Például 4&gt; = 25%. |
| elérhetőség [0] dataSizeMetric.name |sztring | |
| elérhetőség [0] dataSizeMetric.value |egész szám | |
| elérhetőség [0] durationMetric.name |sztring | |
| elérhetőség [0] durationMetric.value |szám |A vizsgálat időtartama. 1e7==1s |
| elérhetőség [0] üzenet |sztring |Hibadiagnosztika |
| elérhetőség [0] eredmény |sztring |Sikeres/sikertelen |
| elérhetőség [0] runLocation |sztring |A http req földrajzi forrása |
| elérhetőség [0] tesztNév |sztring | |
| elérhetőség [0] tesztRunId |sztring | |
| elérhetőség [0] tesztTimestamp |sztring | |

## <a name="metrics"></a>Mérőszámok
Által generált TrackMetric().

A metrikaérték a context.custom.metrics[0]

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

## <a name="about-metric-values"></a>Metrikus értékek –
Metrikaértékek, mind a metrikajelentésekben, mind máshol, szabványos objektumstruktúrával vannak jelentve. Példa:

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

Jelenleg - bár ez változhat a jövőben - a szabványos SDK-modulokból jelentett összes értékben, `count==1` és csak a és `name` `value` a mezők hasznosak. Az egyetlen eset, ahol más lenne, ha saját TrackMetric hívásokat ír, amelyekben beállítja a többi paramétert.

A többi mező célja, hogy lehetővé tegye a metrikák összesítettsítése az SDK-ban, hogy csökkentse a portálra irányuló forgalmat. Például több egymást követő leolvasást átlagolhat az egyes metrikajelentések elküldése előtt. Ezután kiszámítja a min, max, szórásés és összesített értéket (összeg vagy átlag), és állítsa be a jelentést a leolvasások számát.

A fenti táblákban elhagytuk a ritkán használt mezők számát, a min, max, stdDev és a sampledValue mezők számát.

A metrikák előzetes összesítése helyett [mintavételezést](../../azure-monitor/app/sampling.md) is használhat, ha csökkentenie kell a telemetriai adatok mennyiségét.

### <a name="durations"></a>Időtartamok
Eltérő rendelkezés hiányában az időtartamok tized mikroszekundumban jelennek meg, így az 10000000,0 1 másodpercet jelent.

## <a name="see-also"></a>Lásd még
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Folyamatos exportálás](export-telemetry.md)
* [Kódminták](export-telemetry.md#code-samples)
