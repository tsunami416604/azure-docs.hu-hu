---
title: Azure Application Insights naplóalapú metrikák | Microsoft dokumentumok
description: Ez a cikk az Azure Application Insights-metrikákat, támogatott összesítéseket és dimenziókat sorol fel. A naplóalapú metrikák részletei közé tartozik az alapul szolgáló Kusto lekérdezési utasítások.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664915"
---
# <a name="application-insights-log-based-metrics"></a>Az Application Insights naplóalapú mérőszámai

Az Application Insights naplóalapú metrikái lehetővé teszik a figyelt alkalmazások állapotának elemzését, hatékony irányítópultok létrehozását és riasztások konfigurálását. Kétféle metrika létezik:

* A jelenet [mögötti naplóalapú mérőszámok](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) a tárolt események [Kusto-lekérdezéseivé](https://docs.microsoft.com/azure/kusto/query/) lesznek fordítva.
* [A szabványos metrikák](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) előre összesített idősorozatként tárolódnak.

Mivel *a szabványos metrikák* a gyűjtés során előre összesítve vannak, jobb teljesítményt kapnak a lekérdezési időben. Ez teszi őket egy jobb választás az irányítópultok és a valós idejű riasztási. A *naplóalapú metrikák* több dimenzióval rendelkeznek, ami az adatelemzés és az ad-hoc diagnosztika kiváló beállítását jelenti. A [névtér választóval](metrics-getting-started.md#create-your-first-metric-chart) válthat a naplóalapú és a [metrikakezelő](metrics-getting-started.md)szabványos metrikái között.

## <a name="interpret-and-use-queries-from-this-article"></a>A cikkből származó lekérdezések értelmezése és használata

Ez a cikk a támogatott összesítéseket és dimenziókat tartalmazó mutatókat sorolja fel. A naplóalapú metrikák részletei közé tartozik az alapul szolgáló Kusto lekérdezési utasítások. A kényelem érdekében minden lekérdezés az idő részletességének, a diagramtípusnak és néha a dimenzió felosztásának alapértelmezéseit használja, ami leegyszerűsíti a lekérdezés használatát a Log Analytics szolgáltatásban módosítás nélkül.

Ha ugyanazt a mérőszámot ábrázolja a [metrikakezelőben,](metrics-getting-started.md)nincsenek alapértelmezések – a lekérdezés dinamikusan módosul a diagram beállításai alapján:

- A kiválasztott **időtartomány** tágana egy további *időbélyeg...* záradék, amely csak a kiválasztott időtartományból választja ki az eseményeket. A legutóbbi 24 óra adatait megjelenítő diagram például a *következőt tartalmazza: | ahol az időbélyeg > ezelőtt(24 óra).*

- A kiválasztott **idő granularitás** kerül a végső *összefoglalni ... bin(időbélyeg, [időgabona])* záradék.

- A kiválasztott **szűrődimenziók** at további *helyeket* eredményeznek, ahol záradékok.

- A kijelölt **Felosztási diagram** dimenziót a program egy extra összegző tulajdonságra fordítja. Ha például a diagramot *hely*szerint osztja fel, és 5 perces időrészletességgel ábrázolja, az *összegző* záradék összegzése *... bin(időbélyeg, 5 m), hely*.

> [!NOTE]
> Ha most a Kusto lekérdezési nyelv, először másolja és illessze be a Kusto-utasítások a Log Analytics lekérdezési ablaktáblán módosítások nélkül. Az alapdiagram megtekintéséhez kattintson a **Futtatás** gombra. Ahogy elkezdi megérteni a lekérdezési nyelv szintaxisát, elkezdheti a kisebb módosításokat, és láthatja a módosítás hatását. A saját adatok feltárása nagyszerű módja annak, hogy a [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) és az [Azure Monitor](../../azure-monitor/overview.md)teljes erejét megvalósítsa.

## <a name="availability-metrics"></a>Rendelkezésre állási mutatók

Metrikák a rendelkezésre állás i. tárterület lehetővé teszi, hogy a webes alkalmazás állapotát a világ különböző pontjain megfigyelt. [Konfigurálja a rendelkezésre állási teszteket,](../../azure-monitor/app/monitor-web-app-availability.md) hogy ezzel a kategóriával kezdődően elkezdhesse használni a kategória bármely metrikáját.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Elérhetőség (availabilityResults/availabilityPercentage)
A *rendelkezésre állási* metrika azt mutatja, hogy a webes tesztfuttatások százalékos aránya, amely nem észlelt semmilyen problémát. A lehető legalacsonyabb érték 0, ami azt jelzi, hogy az összes webes tesztfuttatás sikertelen volt. A 100-as érték azt jelenti, hogy az összes webes tesztfuttatás megfelelt az érvényesítési feltételeknek.

|Mértékegység|Támogatott összesítések|Támogatott dimenziók|
|---|---|---|---|---|---|
|Százalék|Átlag|Futtatáshelye, Tesztneve|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Rendelkezésre állási teszt időtartama (elérhetőségEredmények/időtartam)

A *rendelkezésre állási teszt időtartama* metrika azt mutatja, hogy mennyi időt vett igénybe a webes teszt futtatásához. A [többlépéses webes tesztek,](../../azure-monitor/app/availability-multistep.md)a metrika az összes lépés teljes végrehajtási idejét tükrözi.

|Mértékegység|Támogatott összesítések|Támogatott dimenziók|
|---|---|---|---|---|---|
|Ezredmásodperc|Átlag, Min, Max|Futtatás helye, Teszt neve, Teszt eredménye

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Rendelkezésre állási tesztek (availabilityResults/count)

A *rendelkezésre állási tesztek* metrika az Azure Monitor által futtatott webes tesztek számát tükrözi.

|Mértékegység|Támogatott összesítések|Támogatott dimenziók|
|---|---|---|---|---|---|
|Darabszám|Darabszám|Futtatás helye, Teszt neve, Teszt eredménye|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Böngészőmérők

A böngésző metrikáit az Application Insights JavaScript SDK valós végfelhasználói böngészőkből gyűjti. Nagyszerű betekintést nyújtanak a felhasználók webalkalmazással kapcsolatos élményébe. A böngésző metrikái általában nem mintavételezésre kerülnek, ami azt jelenti, hogy nagyobb pontosságot biztosítanak a használati számokhoz képest, mint a kiszolgálóoldali metrikák, amelyek mintavételezéssel elferdülhetnek.

> [!NOTE]
> A böngészőmetrikák gyűjtéséhez az alkalmazásnak az [Application Insights JavaScript SDK-val](../../azure-monitor/app/javascript.md)kell rendelkeznie.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Böngészőoldal betöltési ideje (browserTimings/totalDuration)

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|
|---|---|---|
|Ezredmásodperc|Átlag, Min, Max|None|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Az ügyfél feldolgozási ideje (browserTiming/processingDuration)

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|
|---|---|---|
|Ezredmásodperc|Átlag, Min, Max|None|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Lapbetöltési hálózati csatlakozási idő (browserTimings/networkDuration)

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|
|---|---|---|
|Ezredmásodperc|Átlag, Min, Max|None|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Válaszadási idő fogadása (browserTimings/receiveDuration)

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|
|---|---|---|
|Ezredmásodperc|Átlag, Min, Max|None|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Kérelem küldése ideje (browserTimings/sendDuration)

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|
|---|---|---|
|Ezredmásodperc|Átlag, Min, Max|None|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Hibametrikák

A hibák **metrikái** a kérelmek feldolgozásával, a függőségi hívásokkal és a kiváltott kivételekkel kapcsolatos problémákat jelenítik meg.

### <a name="browser-exceptions-exceptionsbrowser"></a>Böngészőkivételek (kivételek/böngésző)

Ez a metrika a böngészőben futó alkalmazáskódból származó kidobott kivételek számát tükrözi. Csak az ```trackException()``` Application Insights API-hívással nyomon követett kivételek szerepelnek a metrikában.

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|Megjegyzések|
|---|---|---|---|
|Darabszám|Darabszám|None|A naplóalapú verzió **a Sum** aggregációt használja|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Függőségi hívási hibák (függőségek/sikertelenek)

A sikertelen függőségi hívások száma.

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|Megjegyzések|
|---|---|---|---|
|Darabszám|Darabszám|None|A naplóalapú verzió **a Sum** aggregációt használja|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Kivételek (kivételek/számlálás)

Minden alkalommal, amikor bejelentkezik egy kivételt az Application Insights, van egy hívás az SDK [trackException() metódusa.](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) A Kivételek metrika a naplózott kivételek számát mutatja.

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|Megjegyzések|
|---|---|---|---|
|Darabszám|Darabszám|Felhőbeli szerepkör neve, felhőalapú szerepkörpéldány, eszköztípus|A naplóalapú verzió **a Sum** aggregációt használja|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Sikertelen kérelmek (kérelmek/sikertelen)

A *sikertelenként*megjelölt nyomon követett kiszolgálókérelmek száma . Alapértelmezés szerint az Application Insights SDK automatikusan megjelöli az 5xx vagy 4xx HTTP-válaszkódot visszaadó kiszolgálókérelmeket sikertelen kérelemként. Ezt a logikát testreszabhatja a kérelem telemetriai elemének *sikeres* tulajdonságának módosításával egy [egyéni telemetriai inicializálóban.](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|Megjegyzések|
|---|---|---|---|
|Darabszám|Darabszám|Felhőbeli szerepkörpéldány, felhőbeli szerepkör neve, Valós vagy szintetikus forgalom, Teljesítmény kérése, Válaszkód|A naplóalapú verzió **a Sum** aggregációt használja|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Kiszolgálókivételek (kivételek/kiszolgáló)

Ez a mérőszám a kiszolgálói kivételek számát mutatja.

|Mértékegység|Támogatott összesítések|Előre összesített dimenziók|Megjegyzések|
|---|---|---|---|
|Darabszám|Darabszám|Felhőbeli szerepkör neve, felhőbeli szerepkörpéldány|A naplóalapú verzió **a Sum** aggregációt használja|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Teljesítményszámlálók

A **Teljesítményszámlálók** kategória metrikáival érheti el az [Application Insights által gyűjtött rendszerteljesítmény-számlálókat.](../../azure-monitor/app/performance-counters.md)

### <a name="available-memory-performancecountersavailablememory"></a>Rendelkezésre álló memória (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Kivételarány (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP-kérelem végrehajtási ideje (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP-kérelmek aránya (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-kérelmek az alkalmazásvárólistában (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Folyamat processzora (performanceCounters/processCpuPercentage)

A mérőszám azt mutatja, hogy a figyelt alkalmazást üzemeltető folyamat a teljes processzorkapacitás mekkora részét használja fel.

|Mértékegység|Támogatott összesítések|Támogatott dimenziók|
|---|---|---|
|Százalék|Átlag, Min, Max|Felhőalapú szerepkörpéldány

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Io-sebesség feldolgozása (performanceCounters/processIOBytesPerSecond)

|Mértékegység|Támogatott összesítések|Támogatott dimenziók|
|---|---|---|
|Másodpercenkénti bájt|Átlag, Min, Max|Felhőalapú szerepkörpéldány

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Magánbájtok feldolgozása (performanceCounters/processPrivateBytes)

Az adatokhoz lefoglalt nem megosztott memória mennyisége.

|Mértékegység|Támogatott összesítések|Támogatott dimenziók|
|---|---|---|
|Bájt|Átlag, Min, Max|Felhőalapú szerepkörpéldány

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processzoridő (performanceCounters/processorCpuPercentage)

A figyelt kiszolgálópéldányon futó *összes* folyamat processzorfogyasztása.

|Mértékegység|Támogatott összesítések|Támogatott dimenziók|
|---|---|---|
|Százalék|Átlag, Min, Max|Felhőalapú szerepkörpéldány

>[!NOTE]
> A processzoridő-mérőszám nem érhető el az Azure App Servicesben üzemeltetett alkalmazásokhoz. A [Process CPU](#process-cpu-performancecountersprocesscpupercentage) metrika segítségével nyomon követheti az App Servicesben üzemeltetett webalkalmazások cpu-kihasználtságát.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Kiszolgálói mutatók

### <a name="dependency-calls-dependenciescount"></a>Függőségi hívások (függőségek/számlálók)

Ez a metrika a függőségi hívások számához viszonyítva.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Függőség időtartama (függőségek/időtartam)

Ez a metrika a függőségi hívások időtartamára vonatkozik.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Kiszolgálói kérelmek (kérések/számlálás)

Ez a mérőszám a webalkalmazás által fogadott bejövő kiszolgálókérelmek számát tükrözi.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Szerverválaszidő (kérelmek/időtartam)

Ez a metrika azt az időt tükrözi, amíg a kiszolgálók feldolgozták a bejövő kérelmeket.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Használati metrikák

### <a name="page-view-load-time-pageviewsduration"></a>Oldalnézet betöltési ideje (oldalNézetek/időtartam)

Ez a mérőszám arra az időre vonatkozik, amennyi idő betöltéséhez a PageView-események betöltéséhez kellett.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Oldalmegtekintések (oldalmegtekintések/számlálók)

A TrackPageView() Application Insights API-val naplózott PageView események száma.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Munkamenetek (munkamenetek/számlálások)

Ez a metrika a különböző munkamenet-azonosítók számára vonatkozik.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Nyomkövetések (nyomok/szám)

A TrackTrace() Application Insights API-hívással naplózott nyomkövetési utasítások száma.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Felhasználók (felhasználók/számlálók)

Az alkalmazáshoz hozzáférő különböző felhasználók száma. A metrika pontosságát jelentősen befolyásolhatja a telemetriai mintavétel és a szűrés.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Felhasználók, Hitelesített (felhasználók/hitelesített)

Az alkalmazásba hitelesítve lévő különböző felhasználók száma.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
