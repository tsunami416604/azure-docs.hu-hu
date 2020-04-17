---
title: Application Insights API egyéni eseményekhez és metrikákhoz | Microsoft dokumentumok
description: A használat nyomon követéséhez és a problémák diagnosztizálásához szúrjon be néhány sorkódot az eszközbe vagy az asztali alkalmazásba, a weblapra vagy a szolgáltatásba.
ms.topic: conceptual
ms.date: 03/27/2019
ms.openlocfilehash: d6cb2f5ab418e8d3b5935fef535565ccf55a3906
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536947"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights API egyéni eseményekhez és metrikákhoz

Szúrjon be néhány sornyi kódot az alkalmazásba, hogy megtudja, mit csinálnak vele a felhasználók, vagy hogy segítsen diagnosztizálni a problémákat. Telemetriai adatokat küldhet az eszköz- és asztali alkalmazásokból, a webügyfelekből és a webkiszolgálókról. Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) alapvető telemetriai API-t egyéni események és metrikák küldéséhez, valamint a standard telemetriasaját verzióinak küldéséhez használja. Ez az API ugyanaz az API, amelyet a standard Application Insights-adatgyűjtők használnak.

## <a name="api-summary"></a>API összefoglaló

Az alapvető API egységes minden platformon, eltekintve `GetMetric`néhány változat, mint a (.NET csak).

| Módszer | Alkalmazási cél |
| --- | --- |
| [`TrackPageView`](#page-views) |Oldalak, képernyők, pengék vagy űrlapok. |
| [`TrackEvent`](#trackevent) |Felhasználói műveletek és egyéb események. A felhasználói viselkedés nyomon követésére vagy a teljesítmény figyelésére szolgál. |
| [`GetMetric`](#getmetric) |Nulla és többdimenziós metrikák, központilag konfigurált összesítés, csak C#. |
| [`TrackMetric`](#trackmetric) |Teljesítménymérések, például adott eseményekhez nem kapcsolódó várólistahosszok. |
| [`TrackException`](#trackexception) |Kivételek naplózása a diagnózishoz. Nyomon követheti, hogy hol fordulnak elő más eseményekhez képest, és vizsgálja meg a veremnyomkövetéseket. |
| [`TrackRequest`](#trackrequest) |A teljesítményelemzésre vonatkozó kiszolgálói kérelmek gyakoriságának és időtartamának naplózása. |
| [`TrackTrace`](#tracktrace) |Erőforrás-diagnosztikai naplóüzenetek. Külső felek naplóit is rögzítheti. |
| [`TrackDependency`](#trackdependency) |Az alkalmazás által függő külső összetevőkre irányuló hívások időtartamának és gyakoriságának naplózása. |

A telemetriai hívások többségéhez [tulajdonságokat és metrikákat csatolhat.](#properties)

## <a name="before-you-start"></a><a name="prep"></a>Előkészületek

Ha még nem rendelkezik hivatkozással az Application Insights SDK-n:

* Adja hozzá az Application Insights SDK-t a projekthez:

  * [ASP.NET projekt](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Alapprojekt](../../azure-monitor/app/asp-net-core.md)
  * [Java projekt](../../azure-monitor/app/java-get-started.md)
  * [Node.js projekt](../../azure-monitor/app/nodejs.md)
  * [JavaScript minden weboldalon](../../azure-monitor/app/javascript.md) 
* Az eszköz vagy a webkiszolgáló kódjában adja meg a következőket:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic* `Imports Microsoft.ApplicationInsights`

    *Jáva:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Telemetriai ügyfélpéldány beszereznie

Beszerezni egy `TelemetryClient` példányt (kivéve a JavaScript-et a weblapokon):

A [ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) alkalmazások és a nem [HTTP/Worker a .NET/NET](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) Core `TelemetryClient` alkalmazások, javasoljuk, hogy egy példányt a függőségi injektálástároló, ahogy azt a megfelelő dokumentációban.

Ha az AzureFunctions v2+ vagy az Azure WebJobs v3+ szolgáltatást használja , kövesse ezt a dokumentumot:https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Részére akárki látás ez módszer van idejétmúlt üzenet legyen szíves látogat [microsoft/ApplicationInsights-dotnet#1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) részére további részletek.

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemettryClient szálbiztos.

ASP.NET és Java projektek esetén a bejövő HTTP-kérelmek et a rendszer automatikusan rögzíti. Előfordulhat, hogy szeretne létrehozni további példányait TelemettryClient az alkalmazás más moduljához. Például előfordulhat, hogy egy TelemettryClient példány a middleware osztályban az üzleti logikai események jelentéséhez. Beállíthatja a tulajdonságokat, például UserId és DeviceId a számítógép azonosítására. Ez az információ a példány által küldött összes eseményhez kapcsolódik.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

A Node.js projektekben `new applicationInsights.TelemetryClient(instrumentationKey?)` új példány t, de ez csak olyan esetekben ajánlott, amelyek `defaultClient`az egyedülállótól elkülönített konfigurációt igényelnek.

## <a name="trackevent"></a>TrackEvent esemény

Az Application Insightsban az *egyéni esemény* olyan adatpont, amely a [Metrikák Intézőben](../../azure-monitor/platform/metrics-charts.md) összesített számként, a [diagnosztikai keresésben](../../azure-monitor/app/diagnostic-search.md) pedig egyedi előfordulásként jeleníthető meg. (Ez nem kapcsolódik mvc vagy más keret "események.")

A `TrackEvent` különböző események számlálásához helyezze be a hívásokat a kódba. Milyen gyakran választanak a felhasználók egy adott funkciót, milyen gyakran érnek el bizonyos célokat, vagy milyen gyakran követnek el bizonyos típusú hibákat.

Egy játékalkalmazásban például küldjön egy eseményt, amikor egy felhasználó megnyeri a játékot:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Egyéni események az Analytics szolgáltatásban

A telemetria idvan az [Application Insights Analytics](analytics.md) `customEvents` táblázatában érhető el. Minden sor egy hívást `trackEvent(..)` jelöl az alkalmazásban.

Ha [a mintavételezés](../../azure-monitor/app/sampling.md) működik, az itemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például itemCount==10 azt jelenti, hogy a 10 hívások trackEvent(), a mintavételi folyamat csak továbbított a belőlük. Az egyéni események helyes számának beszámításához ezért `customEvents | summarize sum(itemCount)`a kódot kell használnia, például .

## <a name="getmetric"></a>GetMetric (GetMetric)

### <a name="examples"></a>Példák

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric között

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric nem az előnyben részesített módszer metrikák küldéséhez. A metrikákat mindig előre összesíteni kell egy adott időszakban az elküldés előtt. Használja a GetMetric(..) túlterhelések egyikével egy metrikaobjektum leolvasásához az SDK-k előzetes összesítési képességeinek eléréséhez. Ha saját előzetes összesítési logikát valósít meg, a TrackMetric() metódus használatával elküldheti az eredményül kapott összesítéseket. Ha az alkalmazás minden alkalommal külön telemetriai elemet kell küldenie az idők összesítése nélkül, valószínűleg van egy használati eset az esemény telemetriai adatokhoz; lásd: TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

Az Application Insights olyan mutatókat is ábrázolhat, amelyek nem kapcsolódnak adott eseményekhez. Szabályos időközönként figyelheti a várólista hosszát. A mérőszámok náluk az egyes mérések kevésbé érdekesek, mint a változatok és a trendek, így a statisztikai diagramok hasznosak.

Annak érdekében, hogy metrikákat `TrackMetric(..)` küldjön az Application Insightsnak, használhatja az API-t. A metrika kétféleképpen küldhető el:

* Egyetlen érték. Minden alkalommal, amikor egy mérést hajt végre az alkalmazásban, elküldi a megfelelő értéket az Application Insights. Tegyük fel például, hogy rendelkezik egy mérőszámmal, amely leírja a tárolóban lévő elemek számát. Egy adott időszakban először három elemet helyez el a tárolóba, majd eltávolít két elemet. Ennek megfelelően, `TrackMetric` akkor hívja kétszer: `3` először `-2`halad az érték, majd az érték . Az Application Insights mindkét értéket tárolja az Ön nevében.

* Összesítés. A mérőszámokkal végzett munka során minden egyes mérés ritkán érdekes. Ehelyett fontos egy összefoglaló, hogy mi történt egy adott időszakban. Ezt az összefoglalót _összesítésnek nevezzük._ A fenti példában az adott időszak összesített `1` metrikaösszege, `2`és a metrikaértékek száma . Az összesítési megközelítés használatakor `TrackMetric` csak egyszer hív meg időszakonként, és elküldi az összesített értékeket. Ez az ajánlott megközelítés, mivel jelentősen csökkentheti a költségek és a teljesítmény terhelését azáltal, hogy kevesebb adatpontot küld az Application Insightsnak, miközben továbbra is összegyűjti az összes vonatkozó információt.

### <a name="examples"></a>Példák

#### <a name="single-values"></a>Egyedi értékek

Egyetlen metrikaérték küldése:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Egyéni mutatók az Analytics szolgáltatásban

A telemetria idvan az [Application Insights Analytics](analytics.md) `customMetrics` táblázatában érhető el. Minden sor egy hívást `trackMetric(..)` jelöl az alkalmazásban.

* `valueSum`- Ez a mérések összege. Az átlagérték leásásához `valueCount`oszd el a szerint.
* `valueCount`- A `trackMetric(..)` hívásba összesített mérések száma.

## <a name="page-views"></a>Oldalnézetek

Egy eszköz vagy weblap alkalmazásban a lapnézet telemetriáját a rendszer alapértelmezés szerint elküldi, amikor minden képernyő vagy oldal betöltődik. Ezt azonban módosíthatja az oldalmegtekintések további vagy eltérő időpontokban való nyomon követéséhez. Például egy olyan alkalmazásban, amely lapokat vagy paneleket jelenít meg, előfordulhat, hogy nyomon szeretne követni egy oldalt, amikor a felhasználó új panelt nyit meg.

A felhasználói és munkamenet-adatok at az oldalnézetekkel együtt tulajdonságokként küldik el, így a felhasználó és a munkamenet-diagramok életre kelnek, amikor oldalnézet-telemetria van.

### <a name="custom-page-views"></a>Egyéni oldalnézetek

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Ha több lap van a különböző HTML-oldalakon belül, az URL-t is megadhatja:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Oldalnézetek időzítése

Alapértelmezés szerint az **oldalnézet betöltési idejeként** jelentett időpontokat a böngésző a kérelem elküldésekor méri, egészen addig, amíg meg nem hívják a böngésző oldalbetöltési eseményét.

Ehelyett a következőket teheti:

* Explicit időtartam beállítása a [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) hívásban: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Használja az oldalnézet `startTrackPage` `stopTrackPage`időzítési hívásait és a .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Az első paraméterként használt név társítja a hívások indítását és leállítását. Alapértelmezés szerint az aktuális lapnév jelenik meg.

Az eredményül kapott oldalbetöltési időtartamok a Metrika-kezelőben a kezdő és leállítási hívások közötti időközből származnak. Ez rajtad múlik, hogy milyen intervallum valójában idő.

### <a name="page-telemetry-in-analytics"></a>Oldaltelemetria az Analytics szolgáltatásban

Az [Analytics](analytics.md) két táblázatban a böngészőműveletek adatai láthatók:

* A `pageViews` táblázat az URL-címre és az oldal címére vonatkozó adatokat tartalmaz
* A `browserTimings` tábla az ügyfél teljesítményére vonatkozó adatokat tartalmazza, például a bejövő adatok feldolgozásához szükséges időt

Annak megkereséséhez, hogy a böngésző mennyi ideig tart a különböző oldalak feldolgozásához:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Felfedezni a népszerűségek a különböző böngészők:

```kusto
pageViews
| summarize count() by client_Browser
```

Ha oldalnézeteket szeretne társítani az AJAX-hívásokhoz, csatlakozzon függőségekkel:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest (TrackRequest)

A kiszolgáló SDK-ja a TrackRequest segítségével naplózza a HTTP-kérelmeket.

Azt is megteheti, hogy saját maga, ha azt szeretné, hogy szimulálja a kérelmeket olyan környezetben, ahol nem fut a webszolgáltatás-modul.

A kérelem telemetriai adatainak küldésének ajánlott módja azonban az, hogy a kérelem <a href="#operation-context">műveletkörnyezetként</a>működik.However, the recommended way to send request telemetry is where the request acts as a operation context.

## <a name="operation-context"></a>Működési környezet

A telemetriai elemeket összekapcsolhatja a műveletkörnyezettel való társításával. A szabványos kérelemkövetési modul ezt a kivételek és egyéb események, amelyek a HTTP-kérelem feldolgozása közben küldött. A [Keresés](../../azure-monitor/app/diagnostic-search.md) és [elemzés](analytics.md)szolgáltatásban a műveletazonosító segítségével könnyedén megtalálhatja a kéréshez kapcsolódó eseményeket.

A korrelációt [lásd: Telemetriai korreláció az Application Insightsban.](../../azure-monitor/app/correlation.md)

Telemetriai adatok manuális nyomon követése, a legegyszerűbb módja annak, hogy telemetriai korreláció ezzel a mintával:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

A műveleti környezet beállítása `StartOperation` mellett létrehoz egy telemetriai elemetriai elemet, amelya megadott típusú. A művelet eltüntetésekor vagy explicit módon hívja meg `StopOperation`a telemetriai elemet. Ha telemetriai típusként használja, `RequestTelemetry` annak időtartama a kezdés és a leállítás közötti időzített intervallumra van beállítva.

A működési hatókörön belül jelentett telemetriai elemek az ilyen művelet "gyermekeivé" válnak. A műveleti környezetek egymásba ágyazhatók.

A Keresés ben a műveletkörnyezet a Kapcsolódó elemek lista létrehozásához **használatos:**

![Kapcsolódó elemek](./media/api-custom-events-metrics/21.png)

Az egyéni műveletek nyomon követéséről az [Application Insights .NET SDK](../../azure-monitor/app/custom-operations-tracking.md) segítségével az egyéni műveletek nyomon követéséről további információt az Egyéni műveletek nyomon követésével című témakörben talál.

### <a name="requests-in-analytics"></a>Kérések az Analytics szolgáltatásban

Az [Application Insights Analytics](analytics.md)alkalmazásban `requests` a kérelmek megjelennek a táblázatban.

Ha [a mintavételezés](../../azure-monitor/app/sampling.md) működik, az itemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például itemCount==10 azt jelenti, hogy a 10 hívások trackRequest(), a mintavételi folyamat csak továbbított a belőlük. A kérelmek helyes számának és a kérelemnevek szerint szegmentált átlagos időtartamnak a lekéréshez használjon a következő kódot:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException (TrackException)

Kivételek küldése az Application Insights számára:

* Számolni [őket](../../azure-monitor/platform/metrics-charts.md), mint jelzi a probléma gyakoriságát.
* [Az egyes előfordulások vizsgálata](../../azure-monitor/app/diagnostic-search.md).

A jelentések tartalmazzák a veremnyomkövetéseket.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Az SDK-k számos kivételt automatikusan elkapnak, így nem kell mindig explicit módon meghívnia a TrackException-et.

* ASP.NET: [Írjon kódot a kivételek hez](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [Kivételek fogott automatikusan](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: A kivételek automatikusan kifognak. Ha le szeretné tiltani az automatikus gyűjtést, adjon hozzá egy sort a weblapokba beszúrt kódrészlethez:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Kivételek az Analytics szolgáltatásban

Az [Application Insights Analytics](analytics.md)alkalmazásban kivételek `exceptions` jelennek meg a táblázatban.

Ha [a mintavétel](../../azure-monitor/app/sampling.md) működik, a `itemCount` tulajdonság 1-nél nagyobb értéket jelenít meg. Például itemCount==10 azt jelenti, hogy a 10 hívások trackException(), a mintavételi folyamat csak továbbított a belőlük. A kivételek típusa szerint szegmentált kivételek helyes számának leszámítása érdekében használjon a következő kódot:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A legtöbb fontos verem információ már kibontott külön változók, `details` de lehet húzni szét a szerkezet, hogy minél több. Mivel ez a struktúra dinamikus, az eredményt a várt típusra kell vetni. Például:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Ha kivételeket szeretne társítani a kapcsolódó kérelmekhez, használjon illesztést:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace között

A TrackTrace segítségével diagnosztizálhatja a problémákat egy "zsemlemorzsanyomvonal" elküldésével az Application Insights számára. Diagnosztikai adatok at küldhet, és megvizsgálhatja azokat a [Diagnosztikai keresés ben.](../../azure-monitor/app/diagnostic-search.md)

A .NET [naplóadapterek](../../azure-monitor/app/asp-net-trace-logs.md) ezzel az API-val küldik a külső naplókat a portálra.

Java [standard naplózók, például a Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) használja Application Insights Log4j vagy Logback Appenders külső naplók küldése a portálra.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*Ügyfél-/böngészőoldali JavaScript*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Diagnosztikai esemény naplózása, például metódus bevitele vagy elhagyása.

 Paraméter | Leírás
---|---
`message` | Diagnosztikai adatok. Sokkal hosszabb is lehet, mint egy név.
`properties` | Karakterlánc leképezése karakterlánchoz: A [kivételek szűréséhez](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) használt további adatok a portálon. Alapértelmezés szerint üres.
`severityLevel` | Támogatott értékek: [SeverityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Az üzenet tartalmában kereshet, de (a tulajdonságértékekkel ellentétben) nem szűrhet rá.

A méretkorlát `message` sokkal magasabb, mint a tulajdonságokra vonatkozó korlát.
A TrackTrace előnye, hogy viszonylag hosszú adatokat helyezhet el az üzenetben. Itt is kódolhatja a POST-adatokat.  

Ezenkívül súlyossági szintet is hozzáadhat az üzenethez. És, mint más telemetriai adatok, tulajdonságértékeket adhat hozzá, hogy segítsen szűrni vagy keresni a különböző nyomkövetések. Például:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

A [Keresés alkalmazásban](../../azure-monitor/app/diagnostic-search.md)ezután könnyedén kiszűrheti az adott adatbázishoz kapcsolódó, adott súlyossági szinthez kapcsolódó összes üzenetet.

### <a name="traces-in-analytics"></a>Nyomkövetések az Animizban

Az [Application Insights Analytics](analytics.md)alkalmazásban a TrackTrace-hívások megjelennek a `traces` táblázatban.

Ha [a mintavételezés](../../azure-monitor/app/sampling.md) működik, az itemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például itemCount==10 azt jelenti, `trackTrace()`hogy a 10 hívások, a mintavételi folyamat csak továbbított a belőlük. A nyomkövetési hívások helyes számának beszedéséhez `traces | summarize sum(itemCount)`ezért a kódot kell használnia, például .

## <a name="trackdependency"></a>TrackDependency (TrackDependency)

A TrackDependency hívás segítségével nyomon követheti a külső kódhoz érkező hívások válaszidejét és sikerességi arányát. Az eredmények a portál függőségi diagramjaiban jelennek meg. Az alábbi kódrészletet hozzá kell adni a függőségi hívás minden esetben.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    RemoteDependencyTelemetry.setTimeStamp(startTime);
    RemoteDependencyTelemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Ne feledje, hogy a kiszolgáló SDK-k tartalmaznak egy [függőségi modult,](../../azure-monitor/app/asp-net-dependencies.md) amely felderít és nyomon követ bizonyos függőségi hívásokat automatikusan – például adatbázisokhoz és REST API-khoz. A modul működéséhez ügynököt kell telepítenie a kiszolgálóra. 

Java-ban bizonyos függőségi hívások automatikusan nyomon követhetők a [Java Agent](../../azure-monitor/app/java-agent.md)használatával.

Ezt a hívást akkor használja, ha olyan hívásokat szeretne nyomon követni, amelyeket az automatikus nyomon követés nem fog, vagy ha nem szeretné telepíteni az ügynököt.

A szabványos függőség-követési modul c#-ban történő kikapcsolásához szerkesztéséhez az `DependencyCollector.DependencyTrackingTelemetryModule` [ApplicationInsights.config fájlból,](../../azure-monitor/app/configuration-with-applicationinsights-config.md) és törölje a hivatkozást a alkalmazásra. A Java, kérjük, ne telepítse a java ügynök, ha nem akarja összegyűjteni a szabványos függőségek automatikusan.

### <a name="dependencies-in-analytics"></a>Függőségek az Analytics szolgáltatásban

Az [Application Insights Analytics](analytics.md)alkalmazásban a trackDependency hívások megjelennek a `dependencies` táblázatban.

Ha [a mintavételezés](../../azure-monitor/app/sampling.md) működik, az itemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például itemCount==10 azt jelenti, hogy a 10 hívások trackDependency(), a mintavételi folyamat csak továbbított a belőlük. A célösszetevő szerint szegmentált függőségek helyes számának leszámítása érdekében használjon a következő kódot:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Ha függőségeket szeretne társítani a kapcsolódó kérelmekhez, használjon illesztést:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Kiürítési adatok

Az SDK általában rögzített időközönként (általában 30 mp) vagy amikor a puffer megtelt (általában 500 elem). Bizonyos esetekben azonban előfordulhat, hogy a puffer kiürítését szeretné használni – például ha az SDK-t egy leállított alkalmazásban használja.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

A függvény aszinkron a [kiszolgáló telemetriai csatornához.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)

Ideális esetben a flush() módszert kell alkalmazni az Alkalmazás leállítási tevékenységéhez.

## <a name="authenticated-users"></a>Hitelesített felhasználók

Egy webalkalmazásban a felhasználókat (alapértelmezés szerint) cookie-k azonosítják. Előfordulhat, hogy egy felhasználó torkig van egynél többször, ha egy másik gépről vagy böngészőből fér hozzá az alkalmazáshoz, vagy ha törli a cookie-kat.

Ha a felhasználók bejelentkeznek az alkalmazásba, pontosabb számot kaphat, ha a hitelesített felhasználói azonosítót a böngészőkódjában állítja be:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Egy ASP.NET webes MVC alkalmazás, például:

*Borotva*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Nem szükséges a felhasználó tényleges bejelentkezési nevét használni. Csak olyan azonosítónak kell lennie, amely egyedi az adott felhasználó számára. Nem tartalmazhat szóközt vagy `,;=|`karaktereket.

A felhasználói azonosító egy munkamenet-cookie-ban is be van állítva, és elküldésre kerül a kiszolgálónak. Ha a kiszolgáló SDK telepítve van, a hitelesített felhasználói azonosító telemetriai adatok részeként küldi el a rendszer. Ezután szűrheti és kereshet rajta.

Ha az alkalmazás fiókokba csoportosítja a felhasználókat, a fiókazonosítót is átadhat (azonos karakterkorlátozásokkal).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

A [Metrics Explorer alkalmazásban](../../azure-monitor/platform/metrics-charts.md)létrehozhat egy diagramot, amely megszámolja a **Felhasználók, a Hitelesített**és a Felhasználói **fiókokat.**

Adott felhasználónevekkel és fiókokkal rendelkező ügyféladatpontokat is [kereshet.](../../azure-monitor/app/diagnostic-search.md)

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Az adatok szűrése, keresése és szegmentálása tulajdonságok használatával

Tulajdonságokat és mértékeket csatolhat az eseményekhez (és metrikákhoz, oldalnézetekhez, kivételekhez és egyéb telemetriai adatokhoz).

*A tulajdonságok* olyan karakterlánc-értékek, amelyek segítségével szűrheti a telemetriai adatokat a használati jelentésekben. Ha például az alkalmazás több játékot is biztosít, minden eseményhez csatolhatja a játék nevét, hogy láthassa, mely játékok népszerűbbek.

A húrhosszak ontják a 8192-es korlátot. (Ha nagy adattömböket szeretne küldeni, használja a TrackTrace üzenetparaméterét.)

*A metrikák* olyan numerikus értékek, amelyek grafikusan jeleníthetők meg. Előfordulhat például, hogy szeretné látni, hogy a játékosok által elért pontszámok fokozatosan emelkednek-e. A grafikonok szegmentálhatók az eseményhez küldött tulajdonságok szerint, így különböző játékokhoz külön vagy halmozott diagramokat kaphat.

Ahhoz, hogy a metrikus értékek helyesen jelenjenek meg, 0-nál nagyobbnak vagy azzal egyenlőnek kell lenniük.

A [használható tulajdonságok, tulajdonságértékek és metrikák száma korlátozva](#limits) van.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Ügyeljen arra, hogy ne naplózza a személyes azonosításra alkalmas adatokat a tulajdonságokban.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Tulajdonságok és mutatók beállításának alternatív módja

Ha kényelmesebb, egy esemény paramétereit egy külön objektumban gyűjtheti össze:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Ne használja fel újra ugyanazt a`event` telemetriai elempéldányt (ebben a példában) a Track*() többszöri hívásához. Ez azt eredményezheti, telemetriai adatok at kell küldeni a helytelen konfiguráció.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Egyéni mérések és tulajdonságok az Analytics szolgáltatásban

Az [Analytics](analytics.md)alkalmazásban az egyéni metrikák és tulajdonságok az `customMeasurements` egyes telemetriai rekordok attribútumaiban és `customDimensions` attribútumaiban jelennek meg.

Ha például hozzáadott egy "játék" nevű tulajdonságot a kérelemtelemetriai adatokhoz, ez a lekérdezés megszámolja a "játék" különböző értékeinek előfordulásait, és a "pontszám" egyéni metrika átlagát jeleníti meg:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Figyelje meg, hogy:

* Amikor kibont egy értéket az egyéniDimenziók vagy customMeasurements JSON, dinamikus típusú, és így meg kell leadni, `tostring` vagy `todouble`.
* A [mintavétel](../../azure-monitor/app/sampling.md)lehetőségének figyelembevétele érdekében `sum(itemCount)`a `count()`lehetőséget kell használnia, nem pedig .

## <a name="timing-events"></a><a name="timed"></a>Időzítési események

Néha meg szeretné ábrázolni, hogy mennyi ideig tart egy művelet végrehajtása. Előfordulhat például, hogy tudni szeretné, hogy a felhasználók nak mennyi ideig tart a játékban a választási lehetőségek mérlegelése. Ehhez használhatja a mérési paramétert.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Az egyéni telemetria alapértelmezett tulajdonságai

Ha azt szeretné, hogy az alapértelmezett tulajdonság értékeket néhány egyéni események, amelyeket ír, beállíthatja őket egy TelemetryClient példányban. Az ügyféltől küldött telemetriai elemhez kapcsolódnak.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Az egyes telemetriai hívások felülbírálhatják a tulajdonságszótáraikban lévő alapértelmezett értékeket.

*JavaScript webes ügyfelek*használata JavaScript telemetriai inicializálók.

*Ha tulajdonságokat szeretne hozzáadni az összes telemetriai elemihez,* beleértve a szabványos gyűjtőmodulokból származó adatokat is, [valósítsa meg a végrehajtását. `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties)

## <a name="sampling-filtering-and-processing-telemetry"></a>Mintavételezés, szűrés és feldolgozástelemetria

Kódot írhat a telemetriai adatok feldolgozásához, mielőtt az SDK-ból küldené. A feldolgozás magában foglalja a szabványos telemetriai modulok, például a HTTP-kérelmek gyűjtése és a függőségi gyűjtemény küldött adatokat.

[Tulajdonságok hozzáadása](../../azure-monitor/app/api-filtering-sampling.md#add-properties) a telemetriai adatokhoz a megvalósításával. `ITelemetryInitializer` Hozzáadhat például verziószámokat vagy más tulajdonságokból számított értékeket.

[A szűrés](../../azure-monitor/app/api-filtering-sampling.md#filtering) módosíthatja vagy elvetheti a telemetriai `ITelemetryProcessor`adatokat, mielőtt az SDK-ból elküldené a implementálással. Ön szabályozhatja, hogy mit küld el vagy dob el, de figyelembe kell vennie a mérőszámokra gyakorolt hatást. Az elemek elvetésének módjától függően előfordulhat, hogy nem tud navigálni a kapcsolódó elemek között.

[Mintavételezés](../../azure-monitor/app/api-filtering-sampling.md) egy csomagolt megoldás, amely csökkenti az alkalmazásból a portálra küldött adatok mennyiségét. Ezt anélkül teszi, hogy befolyásolná a megjelenített mutatókat. És ezt anélkül teszi, hogy befolyásolná a problémák diagnosztizálásának képességét a kapcsolódó elemek, például kivételek, kérések és oldalmegtekintések közötti navigálással.

[További információ](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetrialetiltása

A telemetriai adatok gyűjtésének és továbbításának *dinamikus leállítása és elindítása:*

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

A *kijelölt szabványos gyűjtők letiltása*-például teljesítményszámlálók, HTTP-kérések vagy függőségek --törölje vagy véleményezze az [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)megfelelő sorait. Ezt megteheti, például, ha saját TrackRequest adatokat szeretne küldeni.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

A *kijelölt szabványos gyűjtők letiltása*-például teljesítményszámlálók, HTTP-kérések vagy függőségek –- az inicializálás időpontjában a lánckonfigurációs módszerek et az SDK inicializálási kódjához:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

A gyűjtők inicializálás utáni letiltásához használja a Konfiguráció objektumot:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Fejlesztői mód

Hibakeresés során, hasznos, hogy a telemetriai adatok gyorsított a folyamaton keresztül, így azonnal láthatja az eredményeket. További üzeneteket is kap, amelyek segítenek nyomon követni a telemetriai adatokkal kapcsolatos problémákat. Kapcsolja ki éles környezetben, mert lelassíthatja az alkalmazást.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

A Node.js, engedélyezheti a fejlesztői módot, `setInternalLogging` ha `maxBatchSize` engedélyezi a belső naplózást, és 0-ra állítja a beállítást, ami a telemetriai adatok elküldését eredményezi, amint az összegyűjtött.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a>A műszerezési kulcs beállítása a kiválasztott egyéni telemetriai adatokhoz

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dinamikus műszerezési kulcs

A fejlesztési, tesztelési és éles környezetekt tartalmazó telemetriai adatok összekeverésének elkerülése érdekében [létrehozhat külön Application Insights-erőforrásokat,](../../azure-monitor/app/create-new-resource.md ) és módosíthatja a kulcsokat a környezettől függően.

Ahelyett, hogy a műszerezési kulcsot a konfigurációs fájlból kapna, beállíthatja azt a kódban. Állítsa be a kulcsot egy inicializálási módszerben, például global.aspx.cs egy ASP.NET szolgáltatásban:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

A weblapokon érdemes lehet beállítani, hogy a webkiszolgáló állapotát, ahelyett, hogy a kódolás szó szerint a parancsfájlba. Például egy ASP.NET alkalmazásban létrehozott weblapon:

*JavaScript a Razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemettryContext

TelemettryClient rendelkezik egy context tulajdonsággal, amely tartalmazza az összes telemetriai adatokkal együtt küldött értékeket. Ezek általában a szabványos telemetriai modulok által vannak beállítva, de saját maga is beállíthatja őket. Például:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Ha ezeket az értékeket saját maga állítja be, fontolja meg a megfelelő sor eltávolítását az [ApplicationInsights.config fájlból,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)hogy az értékek és a standard értékek ne zavarodhassanak össze.

* **Összetevő**: Az alkalmazás és annak verziója.
* **Eszköz**: Az alkalmazás futtatásának eszközére vonatkozó adatok. (A webalkalmazásokban ez az a kiszolgáló vagy ügyféleszköz, amelyről a telemetriai adatokat küldik.)
* **InstrumentationKey:** Az Application Insights-erőforrás az Azure-ban, ahol a telemetriai adatok jelennek meg. Ez általában felvette ApplicationInsights.config.
* **Hely**: Az eszköz földrajzi helye.
* **Működés**: A webalkalmazásokban az aktuális HTTP-kérelem. Más alkalmazástípusokban beállíthatja, hogy az események et csoportosítsa.
  * **Azonosító:** Olyan generált érték, amely korrelálja a különböző eseményeket, így a diagnosztikai keresés ben bármely eseményt megvizsgálva megkeresheti a kapcsolódó elemeket.
  * **Név**: Azonosító, általában a HTTP-kérelem URL-címe.
  * **SyntheticSource**: Ha nem null vagy üres, egy karakterlánc, amely azt jelzi, hogy a kérelem forrása robotként vagy webes tesztként lett azonosítva. Alapértelmezés szerint a Metrikakezelőszámítás számításaiból kizárt.
* **Tulajdonságok**: Az összes telemetriai adattal küldött tulajdonságok. Az egyes Track* hívásokban felülbírálható.
* **Munkamenet**: A felhasználó munkamenete. Az azonosító egy generált értékre van beállítva, amely akkor módosul, ha a felhasználó egy ideig nem aktív.
* **Felhasználó**: Felhasználói adatok.

## <a name="limits"></a>Korlátok

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Az adatátviteli sebességkorlát elütésének elkerülése érdekében használjon [mintavételezést.](../../azure-monitor/app/sampling.md)

Az adatok megőrzésének meghatározásáról az [Adatmegőrzés és adatvédelem című](../../azure-monitor/app/data-retention-privacy.md)témakörben található.

## <a name="reference-docs"></a>Referenciadokumentumok

* [ASP.NET hivatkozás](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Java-hivatkozás](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [JavaScript-hivatkozás](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>SDK-kód 

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-csomagok](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Kérdések

* *Milyen kivételeket Track_() hívások dobja?*

    Nincs. Nem kell betakarni őket a try-catch záradékok. Ha az SDK problémákba ütközik, akkor naplózza az üzeneteket a hibakeresési konzol kimenetében, és - ha az üzenetek átjutnak - a diagnosztikai keresésben.
* *Van egy REST API-t, hogy adatokat a portálról?*

    Igen, az [adatelérési API.](https://dev.applicationinsights.io/) Az adatok kinyerésének egyéb módjai közé tartozik [az Analytics szolgáltatásból a Power BI-ba történő exportálás](../../azure-monitor/app/export-power-bi.md ) és a folyamatos [exportálás](../../azure-monitor/app/export-telemetry.md).

## <a name="next-steps"></a><a name="next"></a>További lépések

* [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md)
* [Hibaelhárítás](../../azure-monitor/app/troubleshoot-faq.md)
